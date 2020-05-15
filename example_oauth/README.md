# traefik with OAuth of Google

This example is based on [this guide](https://matheja.me/2020/04/10/secure-your-services-with-traefik-and-google-oauth.html).

The goal of this example is to make more secure the access to traefik services with a login on the Google account (and maybe with a 2FA).
This aproach is recommended to the services that you want to make accessible on internet, but there is no access control (login).

1. client lauch the site `whoami.example.com`
2. traefik redirect the client to Google for the login
3. after successfully login on Google traefik check if the user is allow to access
4. client see the website

This example is not like a SSO (Single Sign On), it put a security step before the service is launched.


## Getting Started

### Prerequisites

1. Read the `README.md` on the root folder.
2. Configuration of DNS and SSL certificate like the example `basic`
3. One or multiple Google account

### Installing

1. Create on [console.developers.google.com/apis/credentials](https://console.developers.google.com/apis/credentials) a new project

2. In this project create a new `ID Client OAuth2`

3. Add every container domain where you want to use OAuth `sub.example.org/_oauth`. Add `/_oauth` at the end of every domain. You can put domain that are not reacheable from internet.

4. Put the client ID, client secret in the `.env` file for the variables `GOOGLE_CLIENT_ID` and `GOOGLE_CLIENT_SECRET`

5. Generate a secret with the command and put the result in the `.env` file for the variable `OAUTH_SECRET`

```bash
openssl rand -hex 16
```

6. In the variable `WHITELIST` in the `.env` file you can define the allowed email addresses. If you have a G-Suite account you can make a domain whitelist with the variable `DOMAIN`, but you have to delete the variable `WHITELIST`.

7. Create a new docker container with this [oauth proxy image](https://github.com/thomseddon/traefik-forward-auth). You find the configuration file in `/example-oauth/docker-compose.yml`

```bash
docker-compose up -d oauth
```

8. Add this lables to the docker containers you want to protect (docker-compose syntax). Please make attention with the routers and the middlewares name.

```yaml
# Oauth for whoami
- "traefik.http.routers.whoami.middlewares=whoamisecure"
- "traefik.http.middlewares.whoamisecure.forwardauth.address=http://oauth:4181"
- "traefik.http.middlewares.whoamisecure.forwardauth.authResponseHeaders=X-Forwarded-User"
- "traefik.http.middlewares.whoamisecure.forwardauth.authResponseHeaders=X-Auth-User, X-Secret"
- "traefik.http.middlewares.whoamisecure.forwardauth.trustForwardHeader=true"
```

9. Recreate the container with the command

```bash
docker-compose up -d whoami
```

10. Test the connection with the site `whoami.example.com`, now a redirect will happen first


If you find a problem in this guide or in the configurations files you can open an [issue](https://github.com/frigi83/traefik-examples/issues) on Github. Thanks!
