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
# Website
```dockerfile
version: '3'

services:
  nginx-proxy:
    image: jwilder/nginx-proxy
    restart: always
    ports:
      - "80:80"
      - "443:443"
    volumes:
      - /var/run/docker.sock:/tmp/docker.sock:ro
      - certs:/etc/nginx/certs:ro
      - vhostd:/etc/nginx/vhost.d
      - html:/usr/share/nginx/html
    labels:
      - com.github.jrcs.letsencrypt_nginx_proxy_companion.nginx_proxy

  letsencrypt:
    image: jrcs/letsencrypt-nginx-proxy-companion
    restart: always
    environment:
      - NGINX_PROXY_CONTAINER=nginx-proxy
    volumes:
      - certs:/etc/nginx/certs:rw
      - vhostd:/etc/nginx/vhost.d
      - html:/usr/share/nginx/html
      - /var/run/docker.sock:/var/run/docker.sock:ro

  www:
    image: nginx
    restart: always
    expose:
      - "80"
    volumes:
      - /home/ubuntu/website/main:/usr/share/nginx/html:ro
    environment:
      - VIRTUAL_HOST=lucasjuarez.com,www.lucasjuarez.com
      - LETSENCRYPT_HOST=lucasjuarez.com,www.lucasjuarez.com
      - LETSENCRYPT_EMAIL=contacto@lucasjuarez.com
    depends_on:
      - nginx-proxy
      - letsencrypt
volumes:
  certs:
  html:
  vhostd:
```
