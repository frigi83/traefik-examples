# TCP entry point example
The goal of this example is to use a TCP entry point for a non HTTP service like MQTT.
With the reverse proxy traefik that secure with TLS the connection. In this case, I want to install a mosquitto MQTT server, the container manages the MQTT stuff like the user, password, and the topics. Traefik adds the TLS layer to the TCP connection.

## Getting Started
### Prerequisites
1. Read the `README.md` on the root folder.
2. Configuration of DNS and SSL certificate like the example `basic`

### Installing
1. You have example_basic already functional.

2. Create a folder for the storage of mosquitto configurations, log and data files. Change the definition in the `volumes` parts. In my case, the local folder is: `/volume1/docker/mosquitto`.
    ```bash
    mkdir mosquitto
    cd mosquitto
    mkdir config
    mkdir data
    mkdir log
    ```

3. Copy the configuration file in the `mosquitto/config` folder. In the example file some lines are commented, don't change it for the moment.

4. Create with the `docker-compose.yml` the mosquitto server with the command (maybe you need `sudo` before):
    ```bash
    docker-compose up -d mosquitto
    ```

5. Change the configuration of traefik adding the new TCP entry point and the port binding. In this example the new port is 1883.
    ```yml
    command
      ...
    - "--entryPoints.tcpep.address=:1883"

    ports:
        ...
      - 1883:1883
    ```

6. Recreate the traefik container with the command (maybe you need `sudo` before):
    ```bash
    docker-compose up -d reverse-proxy
    ```

7. The mosquitto MQTT server is now operational, but everyone that knows the address can read or write to the MQTT topics.

8. Login to the shell of the mosquitto container and create a new user
    ```bash
    docker exec -it mqtt sh
    mosquitto_passwd -c /mosquitto/config/credentials USER-CHANGE-THIS
    ```

9. Now you can uncomment these lines in the `mosquitto.conf`, if you want no quest access to the MQTT broker.
    ```bash
    allow_anonymous false
    password_file /mosquitto/config/credentials
    ```

If you find a problem in this guide or in the configurations files you can open an [issue](https://github.com/frigi83/traefik-examples/issues) on GitHub. Thanks!
