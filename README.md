# kin.zone.docker

Create a new Kin.Zone  instance in five minutes using Docker

## What is included ?

| Name | Description | Docker image | Dockerfile |
| -- | -- | -- | -- |
| [Caddy](https://github.com/caddyserver/caddy) | Reverse proxy (create a LetsEncrypt certificate automatically) | [caddy/caddy:2-alpine](https://hub.docker.com/_/caddy) | [Dockerfile](https://github.com/caddyserver/caddy-docker) |
| [Kin.Zone](https://github.com/mkmzone/kin.zone) | Fufusoup by itself | [mkmzone/kin.zone:latest](https://hub.docker.com/repository/docker/mkmzone/kin.zone) | [Dockerfile](https://github.com/mkmzone/kin.zone/blob/master/Dockerfile) |
| [Redis](https://github.com/redis/redis) | In-memory database | [redis:alpine](https://hub.docker.com/_/redis) | [Dockerfile-alpine.template](https://github.com/docker-library/redis/blob/master/Dockerfile-alpine.template) |

## How to use it
- [Install docker](https://docs.docker.com/install/)
- Get searxng-docker
  ```sh
  cd /usr/local
  git clone https://github.com/mkmzone/kin.zone.docker.git
  cd kin.zone.docker
  ```
- Edit the [.env](https://github.com/searxng/searxng-docker/blob/master/.env) file to set the hostname and an email
- Generate the secret key `sed -i "s|ultrasecretkey|$(openssl rand -hex 32)|g" searxng/settings.yml`
- Edit the [searxng/settings.yml](https://github.com/searxng/searxng-docker/blob/master/searxng/settings.yml) file according to your need
- Check everything is working: `docker compose up`
- Run SearXNG in the background: `docker compose up -d`

> [!WARNING]  
> If you use an older version of docker desktop (`< 3.6.0`), you may have to install Docker Compose v1.
> Accordingly, you should modify the commands in this documentation to suit Docker Compose v1. For instance, change 'docker compose up' to 'docker-compose up'.
>
> [Install the docker-compose plugin](https://docs.docker.com/compose/install/#scenario-two-install-the-compose-plugin) (be sure that docker-compose version is at least 1.9.0)

## How to access the logs
To access the logs from all the containers use: `docker compose logs -f`.

To access the logs of one specific container:
- Caddy: `docker compose logs -f caddy`
- SearXNG: `docker compose logs -f searxng`
- Redis: `docker compose logs -f redis`

### Start Kin.Zone with systemd

You can skip this step if you don't use systemd.

- ```cp searxng-docker.service.template searxng-docker.service```
- edit the content of ```WorkingDirectory``` in the ```searxng-docker.service``` file (only if the installation path is different from /usr/local/searxng-docker)
- Install the systemd unit:
  ```sh
  systemctl enable $(pwd)/searxng-docker.service
  systemctl start searxng-docker.service
  ```

## Note on the image proxy feature

The Kin.Zone image proxy is activated by default.

The default [Content-Security-Policy](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Content-Security-Policy) allow the browser to access to ```${SEARXNG_HOSTNAME}``` and ```https://*.tile.openstreetmap.org;```.

If some users wants to disable the image proxy, you have to modify [./Caddyfile](https://github.com/searxng/searxng-docker/blob/master/Caddyfile). Replace the ```img-src 'self' data: https://*.tile.openstreetmap.org;``` by ```img-src * data:;```.

## Multi Architecture Docker images

Supported architecture:
- amd64
- arm64
- arm/v7

## How to update ?

To update the Kin.Zone stack:

```sh
git pull
docker compose pull
docker compose up -d
```

Or the old way (with the old docker-compose version):
```sh
git pull
docker-compose pull
docker-compose up -d
```
