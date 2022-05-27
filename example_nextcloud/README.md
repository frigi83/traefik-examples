# Personal online office with Nextcloud and ONLYOFFICE

This example shows how to configurate [Nextcloud](https://nextcloud.com/) and [ONLYOFFICE](https://www.onlyoffice.com/) behind [Traefik](https://traefik.io/traefik/) reverse proxy


## Prerequisites

1. A domain name, we assume for this example the domain `example.com` (access from internet and SSL certificate).
2. Working basic example
3. Two subdomains pointing to traefik (drive.example.com and office.example.com)

## Container configuration

1. Cange all needed values in the `.env` file

2. Create the Nextcloud container (may need sudo), it will automatic firse generate also the database container

```bash
docker-compose up -d nextcloud
```

3. You can check if the Nextcloud is working by going to the adress: https://drive.example.com

4. Create the ONLYOFFICE container with the command (may need sudo)

```bash
docker-compose up -d onlyoffice
```

5. Check if ONLYOFFICE is started correctly ofening the page: https://office.example.com

6. In Nextcloud you need to install ONLYOFFICE app in the "store" (in the top on the right side you find `+ Apps`)

7. In the settings page the is a new tab `ONLYOFFICE ` on the left add this values:
	- ONLYOFFICE Docs address: https://office.example.com
	- open andvanced server settings
	- ONLYOFFICE Docs address for internal requests: https://office.example.com
	- Save the settings

8. Try to open or create a new document to check if ONLYOFFICE is working.

9. If you want to access Nextcloud and modify the document outside you home network the two domain (drive.example.com and office.example.com) need to be reachable from internet.

If you find a problem in this guide or configurations files you can open an [issue](https://github.com/frigi83/traefik-examples/issues) on GitHub. Thanks!
