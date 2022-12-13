# Iniciar docker-compose
```bash
docker-compose up -d
```
# WireGuard
```dockerfile
version: '3.3'
services:
  wireguard:
    image: linuxserver/wireguard
    container_name: wireguard
    cap_add:
      - NET_ADMIN
      - SYS_MODULE
    environment:
      - PUID=1000
      - PGID=1000
      - TZ=Europe/Madrid
      - SERVERURL= TU_IP_PUBLICA #optional
      - SERVERPORT=51820 #optional
      - PEERS=2 #optional
      - PEERDNS=auto #optional
    volumes:
      - /root/wireguard:/config
      - /lib/modules:/lib/modules
      - /usr/src:/usr/src
    ports:
      - 51820:51820/udp
    sysctls:
      - net.ipv4.conf.all.src_valid_mark=1
    restart: unless-stopped
```
# Samba
```dockerfile
version: "3.3"

services:

  samba:
    image: dperson/samba:rpi
    restart: always
    command: '-u "user;pass" -s "media;/media;yes;no"'
    stdin_open: true
    tty: true
    ports:
      - 139:130
      - 445:445
    volumes:
      - /usr/share/zoneinfo/Europe/Madrid:/etc/localtime
      - ubicacion/de/montaje:/media
```
# Nessus
```dockerfile
version: '3.3'

services:

  nessus:
    image: tenableofficial/nessus
    restart: always
    container_name: nessus
    ports:
      - 8834:8834
```
