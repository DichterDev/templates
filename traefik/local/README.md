# TRAEFIK

A `traefik` template with docker socket proxy.

## Refrences

[Traefik Docs](https://doc.traefik.io/traefik/)
[wollomatic socket-proxy](https://github.com/wollomatic/socket-proxy)
[wollomatic traefik-hardened](https://github.com/wollomatic/traefik-hardened)

## Setup

Edit the `.env` file to your liking.

### Socket

For the `DOCKER_GID` variable you need to execute following command:

```sh
getent group docker | cut -d: -f3
```

Copy and paste this as the variable value.

## Launch

For tls to work correctly you need to deploy this on a valid domain name, if you don't have one and want to use this as a local setup, I would recommend commenting out the tls part in the `config/dynamic/security.yaml` file.

```sh
docker compose up -d
```

## Post

Once the stack finishes starting up, you can access the administration dashboard securely at:

`https://<YOUR_TRAEFIK_DOMAIN>/dashboard/` (Note: The trailing slash is strictly required by Traefik)
