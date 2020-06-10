# Traefik on Docker control access to an external server

With the grove of apps that have Docker container make the use of Traefik simple. But it can append that you have to redirect the traffic to an external server in the network and you can't work with docker labels.  


## Getting Started

### Prerequisites

1. Read the `README.md` on the root folder.
2. Configuration of DNS and SSL certificate like the example `basic`
3. A website outside the Docker environment.

### Installing

1. Add these lines to the Traefik definition in the `docker-compose.yml`. Where `/volume1/docker/traefik/FileProvider/` is a folder on your system that contains the dynamic configuration file for Traefik.

```yaml
command:
  - "--providers.file.directory=/FileProvider/"
volumes:
  - /volume1/docker/traefik/FileProvider/:/FileProvider/  #this line
```

2. In the directy `*/FileProvider/` create a file with file extension `.toml`, in this example is `file-provider.toml`.

```toml
[http]
  # Add the router
  [http.routers]
    [http.routers.plex]
      entryPoints = ["web-secure"]
      service = "service-plex"
      rule = "Host(`site.example.org`)"     #change domain name
      certresolver="certificato"
      [[http.routers.plex.tls.domains]]
        main = "*.example.org"              #change domain name

  # Add the service
    [http.services.service-plex]
      [http.services.service-plex.loadBalancer]
        [[http.services.service-plex.loadBalancer.servers]]
          url = "https://192.168.1.123:32400"
```

3. Change these points in the `.toml` file:

* routers name
* services name
* domain name (rule and certificate)
* url destination (please make attention if you use http or https)


4. Recreate the Traefik container and try in your browser if it works.

```bash
docker-compose up -d reverse-proxy
```

5. If you are not able to reach the site check if the DNS is correct. The answer should give the IP address of the Traefik container.

```bash
nslookup my.example.com
```

6. For security reasons is better to use https between the local server and the Traefik container.

If you find a problem in this guide or in the configurations files you can open an [issue](https://github.com/frigi83/traefik-examples/issues) on GitHub. Thanks!
