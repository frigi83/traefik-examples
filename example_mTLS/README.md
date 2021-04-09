# Client TLS certificate - mutual TLS (mTLS)

This example shows a simple configuration of Traefik with client certificate (mutual RLS or mTLS). [Mutual TLS](https://medium.com/littlemanco/the-magic-of-tls-x509-and-mutual-authentication-explained-b2162dec4401) protect a https service by asking a valid TLS certificate from the client.


## Prerequisites

1. A domain name, we assume for this example the domain `example.com` (access from internet and SSL certificate).
2. Working basic example
3. No proxy and cache from Cloudflare


## Create client certificate

1. Create a certificate authority (CA) with `openssl`

```bash
mkdir ca

# generate private key
openssl genrsa -aes256 -out ca/ca.key 4096

# generate a CA certificate
openssl req -new -x509 -sha256 -days 1095 -key ca/ca.key -out ca/ca.crt
```

2. Now you can generate the client certificate

```bash
mkdir client

# generate private key
openssl genrsa -out client/client.key 4096

# generate a Certificate Signing Request (CSR)
openssl req -new -key client/client.key -sha256 -out client/client.csr

# generate the client certificate
openssl x509 -req -days 365 -sha256 -in client/client.csr -CA ca/ca.crt -CAkey ca/ca.key -set_serial 1 -out client/client.crt

# generate the .p12 (certificate + key)
openssl pkcs12 -export -clcerts -in client/client.crt -inkey client/client.key -out client/client.p12
```

## Traefik configuration

1. Add to the file provider this TLS options:

```toml
[tls.options]
    [tls.options.myTLSOptions]
    [tls.options.myTLSOptions.clientAuth]
        caFiles = ["client.crt"]   # aggiungere tutti i certificati client necessari ["client1","client2"]
        clientAuthType = "RequireAndVerifyClientCert"
```

2. In the filed `caFiles` you can add all your client certificates that you want to allow the access.

3. Add all the client certificates to the Traefik container, the `.crt` file. The `ro` option is for read-only.

```yaml
volumes:
    ...
    - /your/path/cert/client/client.crt:/client.crt:ro
```

4. Recreate the Traefik container with the command:

```bash
docker-compose up -d reverse-proxy
```

## Container configuration

1. Add the follow label to the container that you want to protect:

```yaml
- "traefik.http.routers.CHANGE_ROUTER_NAME.tls.options=myTLSOptions@file"

# !!! change the router name
```

2. Recreate the container, in this example the `whoami` container.

```bash
docker-compose up -d whoami
```

## Test the client certificate

1. You can test with `curl` in the terminal with the command:

```bash
curl -sv https://whoami.example.com
# exit with error

curl -sv https://whoami.example.com --cert client/client.crt --key client/client.key
# you can access the container page
```

2. If the response is ok, you can test with your browser if the page is loaded without the certificate. In Chroma based browser you see an error, but on Firefox nothing appears.

3. Now you can import the certificate into the browser (the `.p12` file), reload the page.

4. In Firefox the first time you are asked to choose the certificate you want to send to the server. The same append in Chrome but every time you boot the browser.


If you find a problem in this guide or configurations files you can open an [issue](https://github.com/frigi83/traefik-examples/issues) on GitHub. Thanks!
