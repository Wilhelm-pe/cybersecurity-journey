# Rate limiting behind a CDN

**Rate limiting** = each client may send at most N requests per second
(plus a short burst). Above that the server answers `429 Too Many Requests`.
It slows down brute force, scraping and directory scanning, while a normal
user (10–30 requests per page load) never notices.

## The trap: who is "the client"?

A reverse proxy (Traefik, Nginx) identifies the client by the IP that connects
to it. Behind a CDN, **every visitor connects through a few CDN IPs**:

```
without CDN:  A (ip1) ─┐
              B (ip2) ─┼─> proxy sees 3 IPs → 3 separate limits
              C (ip3) ─┘
with CDN:     A ─┐
              B ─┼─> CDN (one IP) ─> proxy sees 1 IP → everyone shares one limit
              C ─┘
```

One noisy client then gets **everyone** blocked.

## The fix

The CDN sends the real visitor IP in a header (Cloudflare: `CF-Connecting-IP`).
Tell the proxy to count by that header. Traefik example (Docker labels):

```yaml
- traefik.http.middlewares.api-limit.ratelimit.average=20
- traefik.http.middlewares.api-limit.ratelimit.burst=60
- traefik.http.middlewares.api-limit.ratelimit.sourcecriterion.requestheadername=CF-Connecting-IP
```

**Only trust that header if the origin accepts traffic exclusively from the
CDN.** Otherwise an attacker connects straight to the origin and sends a
different fake header value on every request, getting a fresh limit each time.

## How I tested it (my own staging server)

Client A fires 120 requests at once, then client B sends 10.

| | Client A | Client B |
|---|---|---|
| Before (counted by connecting IP) | 82 × 200, 38 × 429 | **2 × 200, 8 × 429** — blocked because of A |
| After (counted by real-IP header) | 103 × 200, 17 × 429 | **10 × 200** |

```bash
pedido(){ curl -sk -o /dev/null -w '%{http_code}\n' --resolve $H:443:127.0.0.1 -H "CF-Connecting-IP: $1" https://$H/; }
seq 120 | xargs -P 60 -I{} bash -c 'pedido 198.51.100.1' | sort | uniq -c
```

`xargs -P 60` runs 60 requests in parallel; `sort | uniq -c` counts status codes.
