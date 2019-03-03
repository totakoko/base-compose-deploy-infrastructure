# Base Compose Deploy Infrastructure

This project contains a starting point to get your infrastructure running with [compose-deploy](https://github.com/totakoko/compose-deploy).

See the [compose-deploy documentation](https://github.com/totakoko/compose-deploy#getting-started) to know more about running this.


## Content

- `traefik`: [Traefik](https://github.com/containous/traefik) is a HTTP reverse proxy and load balancer.
It acts as the entrypoint of all your web services.
Instead of exposing your containers directly to the world, you will need to add some labels to your containers and they will be exposed automatically using the correct hostname, letting traefik handle all [TLS with Let's Encrypt](https://docs.traefik.io/configuration/acme/).


## CI variables

These environment variables have to be defined in the CI system:
- SSH_HOST
- SSH_FINGERPRINT_BASE64
- SSH_PRIVATE_KEY_BASE64


## Usage

### Initial configuration

- You will probably want to update your ACME email in the [traefik configuration](./traefik/traefik.toml).


### Adding a new stack

- Create a new directory with a `docker-compose.yml` inside.
- If you have special requirements regarding the host system or permissions, add a file called `pre.yml` containing [Ansible tasks](https://docs.ansible.com/ansible/latest/user_guide/playbooks.html)


### Exposing a container

- If you want to expose HTTP/HTTPS ports, use [Traefik labels](https://docs.traefik.io/configuration/backends/docker/#on-containers) to expose your container.

```yaml
...
services:
  ...
  myexposedcontainer:
    ...
    networks:
      ...
      - web # add the web network (used by traefik)
    labels:
      - "traefik.enable=true" # enable traefik for this service
      - "traefik.port=5000" # this is the exposed port of your service
      - "traefik.frontend.rule=Host:test.totakoko.com" # this is the domain/subdomain to access your service
  ...
```

Note that traefik is configured to use the `web` network.

- If you want to expose any other port, then use the map directly

```yaml
...
services:
  ...
  myexposedcontainer:
    ...
    ports:
      - "53:53" # host port / container port
  ...
```


## Roadmap

- Provide a way to backup and restore container volumes (probably using [restic](https://restic.readthedocs.io/en/stable/))
- Provide a way to monitor services
