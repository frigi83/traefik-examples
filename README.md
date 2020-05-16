# traefik examples

In this repo I want to report my experiences with [traefik](https://docs.traefik.io/) in my home network. All the configurations (if possible) are made with [docker-compose](https://docs.docker.com/compose/) so you have less places were you have to mache changes.

In my network I need some services and the most are on `docker` but with the chnge that browsers don't like `http` anymore, I need a way to configure `httpS` for all the services and I don't like to remember the port for every specific site. The solution is a reverse proxy and I have found with `traefik` a nice solution.


## Getting Started

### Examples

- **basic**: installation of traefik with SSL trought Let's Encrypt
- **oauth**: use OAuth to protecta site with Google login


### Installing

1. For each example there is a separate folder, copy this files to your machine

2. In te root directory of this repo there is a variable file `var.env`. Download it and rename in `.env`. The most configurations options specific for yur installation are in this file. You can copy it to every example you want to try.

3. In the `docker-compose.yml` you find all the configuration to generate and configure the docker containers.


### Files & Folders structure of examples

    .
    ├── FileProvider             # folder for dynamic traefik configuration (server not in docker)
    │   └── file-provider.toml   # dynamic config file
    ├── .env                     # variable for docker-compose.yml
    ├── acme.json                # Let's Encrypt SSL certificates
    └── docker-compose.yml


## Help me

If you find a problem in this guide or in the configurations files you can open an [issue](https://github.com/frigi83/traefik-examples/issues) on Github. Thanks!
