# Configuration of the Traefik's dashboard

This example shows how to configure the traefik dashboard with the option `api.insecure=false`.


## Getting Started

### Prerequisites

1. A domain name, we assume for these examples the domain `example.com` (access from internet and SSL certificate).
2. The basic example is working.


### Installing

1. Like all examples, the configuration is in the `docker-compose.yml` file. Follow the `README` in the root directory of this repo.

2. The important part of the `docker-compose.yml` for this example is the follow:

    ```yml
    command:
      - "--api.dashboard=true"
      - "--api.insecure=false"
    labels:
      - "traefik.enable=true"
      - "traefik.http.routers.api.rule=Host(`traefik.${DOMAIN}`)"
      - "traefik.http.routers.api.entrypoints=web-secure"
      - "traefik.http.routers.api.tls.certresolver=certificato"
      - "traefik.http.routers.api.tls.domains[0].main=*.${DOMAIN}"
      - "traefik.http.routers.api.service=api@internal"
      # Add at least an authentication middleware basic or OAuth for a better security
    ```

3. Generate the traefik container with this command (maybe you need sudo).

    ```bash
    docker-compose up -d reverse-proxy
    ```

4. You can reach the dashboard by the address: https://traefik.example.com

### Security

* The connection trough HTTPS is way better than only HTTP, but everyone that can reach this address can see the traefik configuration.

* These are some recommended steps to better dashboard security:

  1. Configure a [basic auth](https://docs.traefik.io/middlewares/basicauth/)

  2. Limit the access to only some IP addresses with a [whitelist](https://docs.traefik.io/middlewares/ipwhitelist/)

  3. Configure OAuth for the authentication (see [example](https://github.com/frigi83/traefik-examples/tree/master/example_oauth))

  4. Disable the dashboard if it is not needed.



If you find a problem in this guide or in the configurations files you can open an [issue](https://github.com/frigi83/traefik-examples/issues) on GitHub. Thanks!
