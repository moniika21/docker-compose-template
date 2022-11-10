# Template for docker compose files

Here is a bunch of docker compose files to deploy with ease some services that I'm used to.

## Nginx proxy manager setup

If your familiar with docker, you will see that some containers have only opened ports (`expose` entry) on the private user defined network, it's because only the [nginx-proxy-manager](https://github.com/NginxProxyManager/nginx-proxy-manager/tree/master)   have ports exposed externally (on the host network card with `ports:` entries) and [handle requests by sub domain name](https://en.wikipedia.org/wiki/Reverse_proxy) and redirect them to desired container or service.

The [Vpn](https://www.wireguard.com/) have also ports exposed externally

## Common attributes

Between all files, they are several common attributes:

### Eventuals logs with the same time zone than my system's logs

```yaml
environment:
      - TZ=Europe/Paris
```

You can also use this method if `/etc/timezone` is present in the host (`ro` is for [read-only](https://docs.docker.com/storage/volumes/#use-a-read-only-volume)).

```yaml
volumes:
      - /etc/timezone:/etc/timezone:ro
```

### Always the latest image

```yaml
image: <name_of_image>:latest
```

### Restart only if the container wasn't stopped manually

```yaml
restart: unless-stopped
```

### Name gave to the container (to be call by this name in the custome bridge network)

```yaml
container_name: <name_desired>
```

### Utilisation of [user defined bridge](https://docs.docker.com/network/bridge/#differences-between-user-defined-bridges-and-the-default-bridge) network to connect all container with, this allow to ping and call a container by it's name instead of it's dynamic ip address

```yaml
networks:
  default:
    name: <name_desired>
    driver: bridge
```

### Inner docker custom volume

- [Default location](https://docs.docker.com/storage/#choose-the-right-type-of-mount) of volumes are `/var/lib/docker/volumes/` (on linux)
- [name](https://docs.docker.com/compose/compose-file/compose-file-v3/#name) is used to override the default naming of real volume in the file system, otherwise the folder name will be like `<directory_name>_<volume_name>`
- The final location of data will be embeded in a `_data` subdirectory folder
- To sum up, the location of the data will be in `/var/lib/docker/volumes/<name_desired>/_data` folder

```yaml
volumes:
  <name_desired>: 
    name: <name_desired>
```

## TODO

- watchtower notification of which container updated, mail or <https://healthchecks.io/>

- update to one update per week
