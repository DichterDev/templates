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

### ACME

For the tls challenge you need to fill in your Let's Encrypt Email in the `.env` file (`TRAEFIK_LETSENCRYPT_EMAIL`).

Traefik itself is running as `2000:2000` on the host (no root), so the `config/acme/acme.json` file needs to be configured so that traefik may access it and the permissions need to be set to `600` for security reasons.

```sh
mkdir config/acme
touch config/acme/acme.json
chown 2000:2000 config/acme/acme.json
chmod 600 config/acme/acme.json
```

## Launch

For tls certificates to work correctly you need to deploy this using an actual public domain address.

```sh
docker network create proxy-net

docker compose up -d
```

## Post

Once the stack finishes starting up, you can access the administration dashboard securely at:

`https://<YOUR_TRAEFIK_DOMAIN>/dashboard/` (Note: The trailing slash is strictly required by Traefik)
