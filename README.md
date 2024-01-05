## PIA-Docker-WireGuard for Synology
### Create a Docker for PIA to use WireGuard

```
Requirements
  - PIA Username/Password
  - Docker
  - Docker-Compose
  - Wireguard Container: https://github.com/wg-easy/wg-easy
  - Port Forward UDP 51820 on Router to Synology

Optional
  Add the following at the end of your Docker Services:
  network_mode: "service:vpn"
  depends_on:
    vpn:
      condition: service_healthy

 The above can be added to a container to make sure it will use only go through the PIA VPN and if it's not up and healthy it will fail.
 Basically you don't send traffic out through your public IP, and only through the VPN.

Example Docker-Compose for qBittorrent which you can add to the current docker-compose.yml
  qbittorrent:
    image: lscr.io/linuxserver/qbittorrent:libtorrentv1
    container_name: qbittorrent
    environment:
      - PUID=${USER_ID}
      - PGID=${GROUP_ID}
      - TZ=${TIMEZONE}
      - WEBUI_PORT=8080
    volumes:
      - ./qbittorrent:/config
      - ${DOWNLOAD_ROOT}:/data/torrents
    restart: always
    healthcheck:
      # Container may fail if the PIA's token expired, so mark as unhealthy when there is no internet connection
      # see: https://github.com/qdm12/gluetun/issues/641#issuecomment-933856220
      test: [ "CMD", "curl", "--fail", "http://127.0.0.1:8080", "https://google.com" ]
      interval: 5s
      retries: 10
    network_mode: "service:vpn"
    depends_on:
      vpn:
        condition: service_healthy
```

Wireguard How-To:
#### https://www.blackvoid.club/wireguard-vpn-for-your-synology-nas/

Run `./pia-getregions.sh` to populate your .env variable for `PIA_LOCATION=`



