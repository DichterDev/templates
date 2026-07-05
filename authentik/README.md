# AUTHENTIK

This is an Authentik docker compose setup template primarily configured using `.env` file. This exists because setting up authentik can be a massive pain. This template also includes an instance of `docker-socket-proxy` for security reasons, the instance is running in a separate network.

## Refrences

- [Authentik Documentation](https://docs.goauthentik.io/install-config/install/docker-compose/)
- [Authentik Docker Socket Proxy](https://docs.goauthentik.io/add-secure-apps/outposts/integrations/docker/#docker-socket-proxy)
- [Linux Server Docker Socket Proxy](https://docs.linuxserver.io/images/docker-socket-proxy/)

## SETUP

Generate Postgres password:

```sh
openssl rand -base64 36
```

Copy and replace `AUTHENTIK_PG_PASSWORD` in `.env`.

Generate Authentik key:

```sh
openssl rand -base64 60
```

Copy and replace `AUTHENTIK_SECRET_KEY` in `.env`.

Also fill in the rest of the `.env` variables with your chosen values.

### TRAEFIK

Make sure that your `auth-server` has access to the same docker network as your traefik instance. It is recommended to create a separate `proxy-network` for this purpose

To configure traefik add the following labels to your `auth-server`.

```sh
labels:
    traefik.enable: true
    traefik.http.routers.authentik.rule: Host(`${AUTHENTIK_DOMAIN}`)
    traefik.http.routers.authentik.entrypoints: websecure
    traefik.http.routers.authentik.tls: true
    traefik.http.routers.authentik.tls.certresolver: ${TRAEFIK_CERT_RESOLVER}
    traefik.http.services.authentik.loadbalancer.server.port: 9000
```

## Launch

After configuring the setup to your needs, you just need to run the containers using docker compose:

```sh
docker compose up -d
```

You can access your instance locally with your `AUTHENTIK_PORT_HTTP` on localhost.

If you configured traefik you may access it on the `AUTHENTIK_DOMAIN` aswell.

## Final Configurations

Because authentik tracks resource outposts at the database layer rather than via system env engine defaults, you must manually explicitly rebind its API tracking endpoint over the proxy interface on initialization.

1. Login
2. Enter the **Admin Interface**
3. **System** --> **Output Integrations**

## Adding an Application

To configure traefik add the following labels to your application.

```sh
labels:
      traefik.enable: true
      traefik.http.routers.targetapp.rule: Host(`myapp.example.com`)
      traefik.http.routers.targetapp.entrypoints: websecure
      traefik.http.routers.targetapp.tls: true
      traefik.http.routers.targetapp.tls.certresolver: ${TRAEFIK_CERT_RESOLVER}
      traefik.http.services.targetapp.loadbalancer.server.port: 8080 # The internal port your app listens on
```

Also don't forget to add the application to the `proxy-network` as with the `auth-server`.
