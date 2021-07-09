# homelab

A repo with information regarding the homelab I use for myself and hopefully some people can learn from some of my head-wrangling experiences.


## Overview of Physical Environment

- UnRaid for storage and the brains of the whole environment.
- OpnSense Firewall
- OpenWRT Router running on R7800
- MB8600 Modem
- Raspberry pi 4 x2 for Pi-Hole + DoH Cloudflare with HA
- Mikrotik Switch

## Docker

- Tools
  - Traefik - used as reverse proxy for both local and remote URLs. Remote is ran behind Cloudflare proxy as well.
  - adminer - A little easier to view internals of various databases
  - ClamAV - Ran once in a while for virus scanning
  - Scrutiny - reads smart data, i just like seeing the webUI over the raw data.
  - mailpie - Still haven't fully figured out a way to backup my emails, I'm using this in the meantime
  - privatebin - I use this to send sensitive info over the internet.

- Backup
  - Duplicacy + B2 - B2 is fairly cheap as a backup solution. I used duplicati initially, but it ran into too many random errors making it somewhat unreliable - Duplicacy isn't perfect either, but it works well. The UI/UX is somewhat lacking, but it's good enough. It's also fairly easy to get up and running and test on another computer for restores
  - Jenkins - I use this for random various jobs - mostly local bakups

- Teslamate - Wonderful tool for people who own teslas.
- Finance
  - firefly + firefly-iii-csv-importer + mysql - A great finance tool for tracking. with the importer, it becomes somewhat automatic. I'll also show some scripts I made to make this a little easier for certain banks
- epicgames-freegames - Pretty cool tool to get free games in epic games if you're into that.

- Notifications
  - rssbridge - I somehow didn't run into this earlier, I was using an online service initially which i hated using. I use this mostly to grab facebook feeds to automate with huginn
  - gotify - To send mobile notifications of any kind
  - healthchecks - Mostly for jenkins and duplicacy to send notifications to gotify.
  - huginn - Various fun automation scripts. A lot of times better to just make a bot in python, but this is more for people who don't want to code.

- Data Visualization
  - Chronograf - visualizes influxDB
  - Prometheus - Use this with pihole statistics and recently replaced telegraf with their node-exporter, since it's a lot more lightweight and easier to use. Not as extensive though
  - Influxdb - time series databaes to store other stuff
  - Grafana - visualize e v e r y t h i n g
  - SpeedFlux - Some tool to do speedtests and put them into influxdb

- Home Automation
  - MQTT - Used with devices that I flash [Tasmota](https://github.com/arendst/Tasmota) on.
  - tasmoadmin - I don't keep this on, but I use it once in a while. The mass-upgrade tool is quite useful when it works
  - Home Assistant - home automation stuff - For example - turn lightbulbs to a yellower light when the sunsets.

- Monica
  - Monica + MySQL - Personal CRM - I mostly use this as my contacts management. I prefer this over Nextcloud.

- Nextcloud + Joplin - Nextcloud mostly for photo storage. Joplin to replace the somewhat lacking nextcloud notes.
- firefox-syncserver - Sync tabs and bookmarks with firefox.

- Media Automation & Management
  - lidarr - Organizing music files - Also useful to see if there's other albums you don't have from a particular artist
  - mango - If you have manga files in electronic form, this is useful
  - alltube - A youtube-dl wrapper on a webUI. Mostly for family members who aren't as technical.
  - Organizr - Combine all services into a 1-stop shop
  - Plex - I use this mostly for music. Moved my other media stuff to jellyfin.
  - Jellyfin - Media server
  - ombi + qbittorrent + jackett + sonarr + Shoko - These are all legitimate projects that can be potentially unethical, but the technology behind these projects are all very cool.


## Some other cool stuff

- Pi-Hole
  - I found this project recently [gravity-sync](https://github.com/vmstan/gravity-sync) which is great for a Primary-Secondary pi-hole setup.
  - After installing gravity-sync, I use keepalived to do a master-backup scheme
  - Related URL: https://www.redhat.com/sysadmin/keepalived-basics
PRIMARY PIHOLE - /etc/keepalived/keepalived.conf
```
vrrp_instance VI_1 {
        state MASTER
        interface eth0
        virtual_router_id 51
        priority 255
        advert_int 1
        authentication {
              auth_type PASS
              auth_pass 12345  # Modify this password to your tastes
        }
        virtual_ipaddress {
              192.168.x.x/32  # Insert your own address here
        }
}
```
SECONDARY PIHOLE - /etc/keepalived/keepalived.conf
```

vrrp_instance VI_1 {
        state BACKUP
        interface eth0
        virtual_router_id 51
        priority 254  # this needs to be lower than the priority above
        advert_int 1
        authentication {
              auth_type PASS
              auth_pass 12345  # Modify this password to be identical to above
        }
        virtual_ipaddress {
              192.168.x.x/32  # Insert the same address/subnet from above
        }
}
```

  - You can type `ip addr` to check your IPs after starting/restarting `keepalived`. You can stop the primary keepalived to verify that the secondary now gets the shared IP. very nice

- Backing up Openwrt
```
ssh root@openwrt-ip 'umask go= && sysupgrade -b /tmp/backup-openwrt-$(date +%F).tar.gz'
scp root@openwrt-ip:"/tmp/backup-openwrt*.tar.gz" /where/you/want/to/save
ssh root@openwrt-ip 'rm /tmp/backup-openwrt*.tar.gz'

# Now only keep the last 3 backups.
find /where/you/want/to/save -type f -printf '%T@\t%p\n' |
    sort -t $'\t' -g | 
    head -n -3 | 
    cut -d $'\t' -f 2- |
    xargs rm || true
```
- Monitoring MB8600 modem
  - I created a project [here](https://github.com/xNinjaKittyx/mb8600) to start monitoring with grafana 

- Discord Bot
  - Not worked on recently, but I use [KoyomiBot](https://github.com/xNinjaKittyx/KoyomiBot) to sometimes replace functionality from huginn.

- Minecraft Server
  - I ran into this [great article](https://terminalblues.dev/Hiding-Your-Minecraft-Source-IP#DNS) on setting up a reverse proxy on a cloud service like digitalocean so that you can 1) use a domain and 2) not have the domain point directly to your personal IP. (Great when you're playing with an extended group of friends without having to give your real IP)
  - One thing that's a little tricky is when you want to do a subdomain that has a different IP than your main domain.
  - Here's how you do it
```
Type   |  Name    | Content     | TTL    | Proxy Status
A      | minecraft| DO ipv4 IP  | Auto   | Proxied

And for the SRV record

Name = play  # for example if you want this to be play.yourdomain.com
service = _minecraft
Protocol = TCP
TTL = Auto
Priority = 0
Weight = 5
Port = 25565
Target = minecraft.urfmode.moe
```
