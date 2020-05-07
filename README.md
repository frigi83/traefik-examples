# traefik examples

Esempi di configurazione tramite [docker-compose](https://docs.docker.com/compose/) per la configurazione del reverse proxy [traefik](https://docs.traefik.io/).

## Getting Started

### Prerequisites

Vi serve un'installazione di docker e docker-compose funzionante. Le porte 80 e 443 non devono essere occupate da altri processi. Configurate sul vostro DNS un dominio (meglio se wildcard) che punti sulla macchina con docker.

Ogni container verrà configurato solo per l'entrypoint https perché nel file di configurazione statica è presente la seguente configurazione nel file ```traefik.toml```

```
[entryPoints]
  [entryPoints.web]
    address = ":80"

    [entryPoints.web.http]
    [entryPoints.web.http.redirections]
      [entryPoints.web.http.redirections.entryPoint]
        to = "web-secure"
        scheme = "https"

  [entryPoints.web-secure]
    address = ":443"
```


### Installing

Prima di tutto dovete rinominare il file ```var.env``` in ```.env``` e modificare secondo il bisogno le variabili presenti nel file.

I container docker possono essere installati ed eseguiti con il seguente comando (a seconda dell'installazione potrebbe servire il comando sudo prima di docker-compose).

```
docker-compose up -d reverse-proxy
```

Sostituendo ```reverse-proxy``` con il nome del servizio è possibile installare il container desiderato. Rilanciare lo stesso comando se sono state fatte delle modifiche al file ```.yml```.
