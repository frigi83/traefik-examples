# Install Guacamole on Docker with Traefik and 2FA

Remotely accessing an SSH terminal or graphics session with VNC/RDP can be very useful. Particularly if you have a lot of devices and if you regularly change your workstation. Guacamole is one of the open source solutions on the market. Unfortunately there is not a single docker container to install and it's not easy to find a complete guide for installation from start to finish.

Below I've created a small guide to install Guacamole with a mysql database for user management and integration with Traefik to have an https connection.


## Prerequisites

1. A domain name, we assume for this example the domain `example.com` (access from internet and SSL certificate).
2. Working basic example
3. Access with SSH to the machine where `docker` is installed


## Installation procedure

### Mysql database

1. Generate initialization file for mysql database (may need sudo)

```bash
docker run --rm guacamole/guacamole /opt/guacamole/bin/initdb.sh --mysql > initdb.sql
```

2. The generated file must be passed (linked as a volume) to the database to import the structure needed for Guacamole

3. Launch and create the database container (you may need sudo)

```bash
docker-compose up -d guacamole-db
```

### Guacamole
1. Create the two Guacamole containers by running the following command (you may need sudo). Given the dependencies of the various containers, both will be created.

```bash
docker-compose up -d guacamole
```
2. Guacamole is up and running and you can connect to the interface at: https://guacamole.example.com First login details are:
	- user: ***guacadmin***
	- password: ***guacadmin***

3. I suggest you duplicate the default account and create a new administrator account. Then login with the new account and delete the default one.

### 2FA for Guacamole

Since version 1.3.0 TOTP is integrated into the docker container ([PR 471](https://github.com/apache/guacamole-client/pull/471)), unfortunately the [documentation](https://guacamole.incubator.apache.org/doc/gug/) has not been updated yet.

1. When the Guacamole installation is working, it is recommended to enable 2-factor authentication (2FA).

2. Add the parameter `TOTP_ENABLED: 'true'` to the `guacamole` container. At the first login you will be presented with the QR to activate 2FA and asked to enter a code to confirm.


If you find a problem in this guide or configurations files you can open an [issue](https://github.com/frigi83/traefik-examples/issues) on GitHub. Thanks!
