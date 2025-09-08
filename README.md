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
- [urbackup](https://github.com/uroni/urbackup_backend) to backup windows computers.
- [bitwarden-secure-sync](https://github.com/AronMarinelli/bitwarden-secure-sync) to backup password manager.

### AI
- [ollama](https://github.com/ollama/ollama) - API for model access.
- [SearXNG](https://github.com/searxng/searxng/) - Not AI, but using this as a interface for the AI to web search via OpenWebUI.
- [Kokoro-FastAPI](https://github.com/remsky/Kokoro-FastAPI) - TTS
- [open-webui](https://github.com/open-webui/open-webui) - A really nice front end and tools for utilizing AI
- And lastly, I have a private rust project to interact with it via discord.

### RSS
- [morss](https://github.com/xNinjaKittyx/morss) - to convert non-RSS feeds into RSS ones (my own fork).
- [rss-bridge](https://github.com/RSS-Bridge/rss-bridge) - Easy way to get rss feeds with better filtering.
- [FreshRSS](https://github.com/FreshRSS/FreshRSS) - WebUI
- 

### Gaming

### Observability
- [scrutiny](https://github.com/AnalogJ/scrutiny) for SMART reports on drives.
- [beszel](https://github.com/henrygd/beszel) for quick insights on desktops/servers.
- [teslamate](https://github.com/teslamate-org/teslamate) ingest data from tesla API.
- [cable-modem-stats](https://github.com/sarabveer/cable-modem-stats/) ingest data from xFinity modems
- [pyapcupsdexporter](https://github.com/xNinjaKittyx/pyapcupsdexporter) ingest data from APC UPS
- [unpoller](https://github.com/unpoller/unpoller) ingest data from Unifi Devices
- [cadvisor](https://github.com/google/cadvisor) ingest data from current server and drives.
- [MySpeed](https://github.com/gnmyt/myspeed) - Simple automated speed tests and historical data.
- [Prometheus](https://github.com/prometheus/prometheus) [Grafana](https://github.com/grafana/grafana) [InfluxDB](https://github.com/influxdata/influxdb) and [Vector](https://github.com/vectordotdev/vector) for routing, graphing, storage, etc.

### Management/IT
- [Portainer](https://github.com/portainer/portainer) to manage remote docker environments
- [pgAdmin4](https://www.pgadmin.org/download/pgadmin-4-windows/) to manage postgres dbs.
- [it-tools](https://github.com/sharevb/it-tools)

### Personal
- [Wallos](https://github.com/ellite/Wallos) - simple subscriptions I pay for overview
- [Monica](https://github.com/monicahq/monica) - personal crm.
- [FireflyIII](https://github.com/firefly-iii/firefly-iii) - finances
- [Dawarich](https://github.com/Freika/dawarich) - Google Maps Timeline alternative
- [DumbAssets](https://github.com/DumbWareio/DumbAssets) - Used to keep track of maintenance tasks.
- [Outline](https://github.com/outline/outline) - wiki of sorts.
- [Karakeep](https://github.com/karakeep-app/karakeep) - Previously known as hoarder. A great bookmarking tool. Previously used [Linkwarden]()
- [Privatebin](https://github.com/PrivateBin/PrivateBin/releases) - For any E2E required text transfer.
- [TheLounge](https://github.com/thelounge/thelounge) - IRC needs.
- [OpenGist](https://github.com/thomiceli/opengist) - any quick saves of text.
- [Vikunja](https://github.com/alpaka-group/vikunja) - Todo list.

### Media
- [Zipline](https://github.com/diced/zipline) - a photo server, I mostly use for ShareX compatibility.

### Random
- [Collabora]() - Nextcloud integration with OpenOffice. Basically a Google Docs Suite alternative.
- [Romm]() - Emulation in the web browser.
- [matrix]() + [element]()


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

