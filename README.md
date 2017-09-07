# docker-image-dns

[![Build Status](https://travis-ci.org/chesszebra/docker-image-dns.svg?branch=master)](https://travis-ci.org/chesszebra/docker-image-dns)

This repository contains the Docker image that can be used to set up 
a local dns server. This DNS server will let you browse to containers
by appending `.docker` to the container name in the browser.

This image is based on https://github.com/jderusse/docker-dns-gen

## Usage

Configure the local DNS Masq by doing the following:

```bash
sudo vim /etc/NetworkManager/dnsmasq.d/01_docker
```

Enter the following text:

```text
bind-interfaces
interface=lo
interface=docker0
server=/docker/127.0.0.1#54
```

Restart the NetworkManager:

```bash
sudo systemctl restart NetworkManager
```

Next start the container and make sure it always restarts:

```bash
docker run --detach --name dns \
  --restart always \
  --publish 54:53/udp \
  --volume /var/run/docker.sock:/var/run/docker.sock \
  chesszebra/dns -R
```

Alternatively, if you are running a more barebones linux without dnsmasq and 
NetworkManager this is probably the easiest way to get it working:

1. Skip the NetworkManager config since it does not apply.
2. Start the container instead with `–publish 53:53/udp`
3. Add `127.0.0.1` to `/etc/resolv.conf`
