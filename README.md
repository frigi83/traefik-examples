# traefik examples

Esempi di configurazione tramite [docker-compose](https://docs.docker.com/compose/) per la configurazione del reverse proxy [traefik](https://docs.traefik.io/).

## Getting Started

### Prerequisites

Vi serve un'installazione di docker e docker-compose funzionante. Le porte 80 e 443 non devono essere occupate da altri processi. Configurate sul vostro DNS un dominio (meglio se wildcard) che punti sulla macchina con docker. La configurazione proposta utilizza Cloudflare come provider DNS per creare un certificato Let's Encrypt wildcard.

Tutta la configurazione di Traefik è presente nel file ```docker-compose.yml```

### File & Folders

    .
    ├── FileProvider             # Directory per i file dinamici del provider file (server non in docker ma nella rete locale)
    │   └── file-provider.toml   # esempio collegamento server non in docker
    ├── acme.json                # Certificati generati da Let's Encrypt
    └── docker-compose.yml


### Installing

1. Prima di tutto dovete rinominare il file ```var.env``` in ```.env``` e modificare secondo il bisogno le variabili presenti nel file.

2. Creare un file ```acme.json``` utilizzando i seguenti comandi:

```
touch acme.json
chmod 600 acme.json
```

3. I container docker possono essere installati ed eseguiti con il seguente comando (a seconda dell'installazione potrebbe servire il comando sudo prima di docker-compose).

```
docker-compose up -d reverse-proxy
```

Sostituendo ```reverse-proxy``` con il nome del servizio è possibile installare il container desiderato. Rilanciare lo stesso comando se sono state fatte delle modifiche al file ```docker-compose.yml```.
