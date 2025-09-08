# homelab

A repo with information regarding the homelab I use for myself and hopefully some people can learn from some of my head-wrangling experiences.

## Currently messing with

- [coder](https://github.com/coder) - In order to manage my code environments rather than just plain VMs.


## Overview

Almost everything here is containerized with the exception of a few things.

### Frontend/Proxy/Etc
- Basically I take [frp](https://github.com/fatedier/frp) and run it on a cheap VPS host.
  - frps running on VPS
  - frpc running on my server
- Cloudflare is pointed to VPS. (basically a roundabout way to do cloudflare tunneling).
  - Thought about using [newt](https://github.com/fosrl/newt), but it doesn't exactly fit my needs because I have certain subdomains that are local-only, and it's not super intuitive on how to do that, if it's even possible.
- frpc -> traefik server that manages the DNS certificate and reverse proxy.
- traefik -> nginx server to manage some edge cases that traefik can't handle.
- Using [imgproxy](https://github.com/imgproxy/imgproxy) with [static-web-server](https://github.com/static-web-server/static-web-server) to handle any random static content.

### DNS
- I use [AdGuardHome](https://github.com/AdguardTeam/AdGuardHome) with [keepalived]() to make a single IP and [adguardhome-sync](https://github.com/bakito/adguardhome-sync/) to sync the instances.
- Then have my OPNSense Router force all DNS traffic in the network to the keepalive IP.

### Auth
- Using [authentik](https://github.com/goauthentik/authentik) to manage SSO across as many of the services I have as possible.

### Automation
- [ntfy](https://github.com/binwiederhier/ntfy) to handle notifications from automated backups or other random notifications.
- [HomeAssistant](https://github.com/home-assistant/core) - I don't use it a lot, just dabbled in some IoT automation. Alongside Tasmota, MQTT, Tasmoadmin, zwave.
- [cronicle-edge](https://github.com/cronicle-edge/cronicle-edge) for some miscellaneous automation.
- 

### Backup
- Using [BackBlaze B2](https://www.backblaze.com/) and [Duplicacy](https://github.com/gilbertchen/duplicacy) for cloud backup.
- Duplicacy is also used to do a local backup to a 2nd server that's running [TrueNAS](https://www.truenas.com/) on [Proxmox VM](https://www.proxmox.com/en/)
- [Immich](https://github.com/immich-app/immich) and [NextCloud](https://github.com/nextcloud) for phone automated photo backups.
- [Syncthing](https://github.com/syncthing/syncthing) as a quick way to get files from one device to another.
- I also host a local [gitea](https://github.com/go-gitea/gitea) server to hold some automation scripts and also to hold a backup from github. 

### AI
- [ollama](https://github.com/ollama/ollama) - API for model access.
- [SearXNG](https://github.com/searxng/searxng/) - Not AI, but using this as a interface for the AI to web search via OpenWebUI.
- [Kokoro-FastAPI](https://github.com/remsky/Kokoro-FastAPI) - TTS
- [open-webui](https://github.com/open-webui/open-webui) - A really nice front end and tools for utilizing AI
- And lastly, I have a private rust project to interact with it via discord.

### RSS
- [morss]()
- [rss-bridge]()
- [FreshRSS]()
- 

### Gaming

### Observability

### Management

### Personal

### Media



### (old) Will update later...


- Teslamate - Wonderful tool for people who own teslas.
- Finance
  - firefly + firefly-iii-csv-importer + mysql - A great finance tool for tracking. with the importer, it becomes somewhat automatic. I'll also show some scripts I made to make this a little easier for certain banks

- Observability
  - Chronograf - Easy way to manage influxDB.
  - Prometheus - Queries various URLs to fetch data.
  - Influxdb - time series databaes to store other stuff
  - Grafana - visualize e v e r y t h i n g
  - caAdvisor - Ingest Docker statistics.
  - Scrutiny - SMART data visualizer

- Monica
  - Monica + MySQL - Personal CRM - I mostly use this as my contacts management. I prefer this over Nextcloud.

- Media Automation & Management
  - lidarr - Organizing music files - Also useful to see if there's other albums you don't have from a particular artist
  - Kavita - If you have manga files or e-books, this is useful for reading that via a web-ui.
  - Plex - I use this mostly for music. Moved my other media stuff to jellyfin.
  - Jellyfin - Media server
  - jfa-go - Manages Jellyfin users.
  - Jellyfin-vue - a cool project for upcoming UI changes for Jellyfin
  - Jellyfin-discord-bot - another cool project to stream jellyfin audio media to discord via casting.
  - jellyseer, qbittorrent, prowlarr, sonarr, Shoko, Kaizoku - All open source projects with questionable intentions, but still very cool.

## Some other cool stuff


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

