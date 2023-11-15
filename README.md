# localssl
Equip local dev server with a CA-signed SSL certificate.

## What is this?

This will enable your local HTTP server to be reachable at a global domain name instead of localhost, e.g. local.example.com, with a valid CA-signed SSL certificate. This is helpful when you need to reach your local server from another device in the same local network _and_ SSL is enabled. A self-signed SSL certificate is not helpful because the cert is only valid on the machine where the server runs.

## How does it work?

## Instruction

1. Get a domain name
2. Get the local IP address of the machine where the HTTP server will run. On macOS, either run `ipconfig getifaddr en0` or go to Settings -> Networks -> Details on the active network -> IP address.
3. Set an `A` record at the desired host on this IP address in your DNS provider's settings. For instance, if you own the domain `example.com` and want your local server running on machine `192.168.0.213` to be reachable at `local.example.com`, set these two records:
```
A local 192.168.0.213
A *.local 192.168.0.213
```
The second one is optional but practical when you have multiple services running and don't want to set a record for each of them.

4. In `docker-compose.yml`, replace all instances of `your-domain.com` with the one that you set the DNS records for, e.g. `local.example.com`. Also replace `CF_API_KEY` and `CF_API_EMAIL` value with your credentials for Cloudflare. If you have another provider, follow this [instruction](https://doc.traefik.io/traefik/https/acme/#providers).

5. Go to `traefik.yml`, update `certificatesResolvers.myresolver.acme.dnsChallenge.provider` if needed
6. Go to `traefik-dynamic.yml`, update the host at `http.routers.to-dev-server.rule` if wanted and update the port at `services.dev-server.loadBalancer.servers[0].url` to the port of your local server
7. Run `docker compose up`. It takes a bit to request a SSL certificate and then your local server should be reachable at the desired host.