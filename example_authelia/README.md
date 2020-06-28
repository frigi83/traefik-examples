# Authelia example

The goal of this example is to make more secure access to traefik services with a login on a central server based on Authelia with the ability to use 2FA.
This approach is recommended to the services that you want to make accessible on the internet, but there is no access control (login), or where you want a single login for multiple services (SSO).

1. client launches the site `whoami.example.com`
2. traefik redirect the client to Authelia server for the login
3. if necessary 2FA step
4. the client sees the website.

## what is Authelia?
Authelia is an open-source authentication server, with Single Sign-On (SSO) and 2 Factor Authentication (2FA). With Authelia you can deploy an authentication server in your network and not relying on external services (like Google, GitHub, ...).


## Getting Started

### Prerequisites

1. Read the `README.md` on the root folder.
2. Configuration of DNS and SSL certificate like the example `basic`
3. A mail server that supports TLS and one account to send mails

### Installing

1. You have example_basic already functional.

2. Update the `.env` file with the new variable: `TIME_ZONE`. You can check on [Wikipedia](https://en.wikipedia.org/wiki/List_of_tz_database_time_zones) which is yours.

3. Create a folder for the storage of Authelia configurations files. Change the definition in the `volumes` parts. In my case, the local folder is: `/volume1/docker/authelia`.

4. In this folder copy the `configuration.yml` file and change the needed values. The mail server needs to support the TLS, otherwise the server doesn't start.

5. This is a lite installation of Authelia and the users are defined in a file. The passwords are hashed with the `sha512`, that is not the default choice. But the default has currently a [bug in a library](https://github.com/authelia/authelia/issues/845) that increases enormously the reserved RAM (from 10-20 MB it goes up to 1 GB).

6. Create in the same folder as `configuration.yml` a file `users_database.yml` with all the users that you need. In the folder `example_authelia/authelia/` you can find two examples. In this case, there is a user `myuser` with a super secret password `password`.

7. On Linux you can generate the hash password with this command:

    ```bash
    openssl passwd -6 YOUR_PASSWORD #sha512
    ```

8. Create with the `docker-compose.yml` the Authelia server with the command (maybe you need `sudo` before):

    ```bash
    docker-compose up -d authelia
    ```

9. The URL to reach the login page is: `login.example.com`

10. Now you can add the following labels for every container that you want to protect with Authelia. You have to change also the `configuration.yml` file.

    ```yml
    - 'traefik.http.routers.CONTAINER_ROUTER.middlewares=authelia@docker'
    ```

11. Or you can copy all this lables for every container:

    ```yml
    - 'traefik.http.middlewares.YOUR_NAME.forwardauth.address=http://authelia:9091/api/verify?rd=https://login.${DOMAIN}/'
    - 'traefik.http.middlewares.YOUR_NAME.forwardauth.trustForwardHeader=true'
    - 'traefik.http.middlewares.YOUR_NAME.forwardauth.authResponseHeaders=Remote-User, Remote-Groups'
    ```

12. If you have configured the service with the policy `one_factor` the user needs only a password. The second case is `two_factor` and you need to configure a 2FA method, a physical key, or an OTP app. For this case, you need the mail server because the user receives an email with a registration link.

13. With no furder configuration the login process works in this way:
    1. Login on Authelia
    2. If policy configured: 2FA
    3. Redirect back to the container
    4. If there is another login form you have to type user and password (maybe not the same as Authelia)

14. For grafana you can specify a SSO. In this case you make the login on the Authelia server and you are automatically loged in in grafana. To do this you need the follow variable set for grafana:

    ```yml
    - GF_USERS_ALLOW_SIGN_UP=false
    - GF_USERS_ASSIGN_ORG=true
    - GF_USERS_ASSIGN_ORG_ROLE=Editor
    - GF_AUTH_PROXY_ENABLED=true
    - GF_AUTH_PROXY_HEADER_NAME=Remote-User
    - GF_AUTH_PROXY_HEADER_PROPERTY=username
    - GF_AUTH_PROXY_AUTO_SIGN_UP=true
    ```


If you find a problem in this guide or in the configurations files you can open an [issue](https://github.com/frigi83/traefik-examples/issues) on GitHub. Thanks!
