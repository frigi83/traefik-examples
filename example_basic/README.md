# Basic configuration of Traefik

This example show a basic configuration of Traefik with secure connection. I have added also examples for the needed configuration for some containers: grafana, Unifi controller, nodered and influxDB.


## Getting Started

### Prerequisites

1. Domain name, we assume for this examples the domain `example.com` (access from internet and SSL certificate).
2. Cloudflare account
3. A working installation of `docker` and `docker-compose`
4. Port 80 and 443 are not used


### Why Cloudflare

I suggest you to migrate the domain to Cloudflare for the follow reasons:
- hide your home IP address
- Dyndns provider (if your home IP change frequently)
- can provide a SSL certificate

If you chose Cloudflare or an other DNS provider you need to add the follow CAA entry to DNS table

```
CAA example.com   0 issue letsencrypt.org
```
It define that Let's Encrypt is allowed to generate a certificate for `example.com`.


### Installing

1. All the configuration are in the `docker-compose.yml` file

2. Copy from the root folder the `var.env` file and rename it in `.env`

3. Change all the needed variables with the values of your case. The follow list show the needed for traefik and whoami test contianer:

```yml
DOMAIN              #domain name
CLOUDFLARE_EMAIL    #Cloudflare email account
CLOUDFLARE_API_KEY  #Cloudflare API key
ACME_EMAIL          #Reminder email for the generated certificate
```

4. Create an empty `acme.json` file to store the certificates

```bash
touch acme.json
chmod 600 acme.json
```

5. Correct the directory where is the `acme.json` in the `docker-compose.yml`

```yml
/volume1/docker/traefik/acme.json:/acme.json  #change the /volume1/docker/traefik/ with your path
```

6. Generate the traefik container with this command (maybe you need sudo)

```bash
docker-compose up -d reverse-proxy
```

7. Generate the whoami container with this command (maybe you need sudo)

```bash
docker-compose up -d whoami
```

8. Try now to reach the whoami container `whoami.example.com`, your are prompted with a warning that the certificate is not valid. No problem! Check via the browser padlock if your certificate is issued by Let's Encrypt, if you have traefik than you have to wait (check the traefik log). Let's Encrypt has a staging server for test purposes that has less time limitations.

9. If the certificate is from Let's Encrypt you can now switch to the production server commentig the follow line in the `docker-compose.yml` with the char `#`

```yml
- "--certificatesresolvers.certificato.acme.caserver=https://acme-staging-v02.api.letsencrypt.org/directory"
```

10. Before launching the traefik container delete e recreate the `acme.json` file

```bash
rm acme.json
touch acme.json
chmod 600 acme.json
```

11. Relaunch the traefik with the command

```bash
docker-compose up -d reverse-proxy
```

12. Try now with different containers like `grafana` or `nodered`

```bash
docker-compose up -d xxx  #change xx with grafana or nodered
```

If you find a problem in this guide or in the configurations files you can open an [issue](https://github.com/frigi83/traefik-examples/issues) on Github. Thanks!
