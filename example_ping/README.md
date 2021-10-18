# HTTP ping to check traefik status

In this example I want to check if traefik is ok with a HTTP ping and save the history on [uptime kuma](https://github.com/louislam/uptime-kuma)


## Getting Started

### Prerequisites

1. A domain name, we assume for this example the domain `example.com` (access from internet and SSL certificate).
2. Basic example working with a certificate

### Why ping
With uptime kuma you can call a https service on your trafik to check the end-to-end path. But you need to load all the page to know if the service is ok. With a ping the responce is very simple and small (= very quick).


### Installing

1. Update the configuration of your traefik container with the informations that are in the `docker-compose.yml`

2. Regenerate the container with the command (if you have an errore add sudo at the begginning of all bash commands):

```bash
docker-compose up -d reverse-proxy
```

3. Create a new folder for all the data for uptime kuma (in my case: `/volume1/docker/uptime-kuma`)

4. Generate the uptime kuma container:

```bash
docker-compose up -d uptime-kuma
```

5. Create a new account and add a new service monitoring. The type is `HTTP(s)` and the address is: `http://traefik.example.com:8088/ping`


If you find a problem in this guide or configurations files you can open an [issue](https://github.com/frigi83/traefik-examples/issues) on GitHub. Thanks!
