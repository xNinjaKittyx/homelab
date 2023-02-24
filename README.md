# homelab

A repo with information regarding the homelab I use for myself and hopefully some people can learn from some of my head-wrangling experiences.


## Overview of Physical Environment
## Docker

- Tools
  - Traefik - used as reverse proxy for both local and remote URLs. Remote is ran behind Cloudflare proxy as well.
  - adminer - A little easier to view internals of various databases
  - ClamAV - Ran once in a while for virus scanning
  - privatebin - I use this to send sensitive info over the internet.
  - Jirafeau - This is a quick file drop server.
  - xbrowsersync - Synchronize Bookmarks and History across various devices and browsers.
  - Thunderbird + ProtonMail-Bridge - A way to always have a copy of my emails in thunderbird.
  - Gitea - Used mostly just to git-control my jenkins scripts.
  - code-server - A quick way to modify some of my jenkins scripts
  - SearXNG - An alternative way to search the web.
  - WikiJS - A wiki server. I use it for storing some of my thoughts as well as documentation.
  - Uptime Kuma - Showing uptime of various services. Not really useful if it's running on the same server though.
  - Homepage - Show a homepage of all services.
  - cloudflareddns - used to automatically update cloudflare DNS A records with my server's external IP address.

- Backup
  - Duplicacy + B2 - B2 is fairly cheap as a backup solution. I used duplicati initially, but it ran into too many random errors making it somewhat unreliable - Duplicacy isn't perfect either, but it works well. The UI/UX is somewhat lacking, but it's good enough. It's also fairly easy to get up and running and test on another computer for restores
  - Jenkins - I use this for random various jobs - mostly local bakups
  - github-backup - Backing up Github projects in github form.

- Teslamate - Wonderful tool for people who own teslas.
- Finance
  - firefly + firefly-iii-csv-importer + mysql - A great finance tool for tracking. with the importer, it becomes somewhat automatic. I'll also show some scripts I made to make this a little easier for certain banks
- epicgames-freegames - Pretty cool automation tool to fetch free games in epic games if you're into that.

- Observability
  - Chronograf - Easy way to manage influxDB.
  - Prometheus - Queries various URLs to fetch data.
  - Influxdb - time series databaes to store other stuff
  - Grafana - visualize e v e r y t h i n g
  - caAdvisor - Ingest Docker statistics.
  - Scrutiny - SMART data visualizer
  - Gotify - Notification system to nofiy my phone.

- Home Automation
  - mosquitto - Used with devices that I flash [Tasmota](https://github.com/arendst/Tasmota) on.
  - tasmoadmin - I don't keep this on, but I use it once in a while. The mass-upgrade tool is quite useful when it works
  - Home Assistant - home automation stuff - For example - turn lightbulbs to a yellower light when the sunsets.
  - zwavejs2mqtt - Using zWave with Home assistant.

- Monica
  - Monica + MySQL - Personal CRM - I mostly use this as my contacts management. I prefer this over Nextcloud.

- Nextcloud - Photo storage
- Immich - Currently testing to see if this is better for me than NextCloud.
- firefox-syncserver - Sync tabs and bookmarks with firefox.

- Media Automation & Management
  - lidarr - Organizing music files - Also useful to see if there's other albums you don't have from a particular artist
  - Kavita - If you have manga files or e-books, this is useful for reading that via a web-ui.
  - Plex - I use this mostly for music. Moved my other media stuff to jellyfin.
  - Jellyfin - Media server
  - jfa-go - Manages Jellyfin users.
  - Jellyfin-vue - a cool project for upcoming UI changes for Jellyfin
  - Jellyfin-discord-bot - another cool project to stream jellyfin audio media to discord via casting.
  - jellyseer, qbittorrent, prowlarr, sonarr, Shoko, Kaizoku - All open source projects with questionable intentions, but still very cool.

- Mattermost
  - Backup to discord when discord goes down. Still prone to Cloudflare downtime though (which will take both down).

## Some other cool stuff

- Adguard Home
  - I use 3x PIs with Portainer + Keepalived + Watchtower + AdguardHome.
  - Use adguardsync on unraid server to make all 3 pis stay in sync.
  

- Pi-Hole
  - I found this project recently [gravity-sync](https://github.com/vmstan/gravity-sync) which is great for a Primary-Secondary pi-hole setup.
  - After installing gravity-sync, I use keepalived to do a master-backup scheme
  - Related URL: https://www.redhat.com/sysadmin/keepalived-basics
PRIMARY PIHOLE - /etc/keepalived/keepalived.conf
```
vrrp_script chk_pihole {
    script "/usr/bin/docker exec pihole /usr/local/bin/pihole status | grep enabled"
    interval 5
    fall 2
}

vrrp_instance VI_1 {
        state MASTER
        interface eth0
        virtual_router_id 51
        priority 255
        advert_int 1
        authentication {
              auth_type PASS
              auth_pass susaf
        }
        virtual_ipaddress {
              192.168.5.210/32
        }
        track_script {
              chk_pihole
        }
}
```
SECONDARY PIHOLE - /etc/keepalived/keepalived.conf
```
vrrp_script chk_pihole {
    script "/usr/bin/docker exec pihole /usr/local/bin/pihole status | grep enabled"
    interval 5
    fall 2
}

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
        track_script {
              chk_pihole
        }
}
```

  - You can type `ip addr` to check your IPs after starting/restarting `keepalived`. You can stop the primary keepalived to verify that the secondary now gets the shared IP. very nice
  - Note - I ran into a problem with 2.1.5 having a bug with this. If you notice that chk_pihole doesn't run, make sure your keepalived version isn't 2.1.5

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
