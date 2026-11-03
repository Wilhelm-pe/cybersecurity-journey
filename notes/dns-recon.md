# DNS recon: `dig`, `nslookup`, `whois`

Learn the tool by the **question it answers**.

| Question | Tool | Why this one |
|---|---|---|
| Which IP is behind this name? | `dig +short name` | Prints only the answer, easy to reuse in other commands |
| Same question, on Windows | `nslookup name` | Ships with Windows; `dig` does not |
| Who owns this IP or domain? | `whois ip` | Shows organization, country, hosting provider |
| Which subdomains exist? | crt.sh, DNSDumpster | Public certificate logs and DNS datasets (passive) |

## `dig` record types

```bash
dig +short example.com A      # IPv4
dig +short example.com AAAA   # IPv6
dig +short example.com MX     # mail servers
dig +short example.com NS     # who hosts the DNS zone
dig +short example.com TXT    # SPF / site verifications (often leak which services are used)
```

Without `+short`, `dig` prints the full report: question, answer, TTL and which
resolver answered. Useful to study; `+short` is for when you only need the value.

## Gotchas I hit

- DNS takes **names**, not URLs: `dig example.com`, not `dig example.com/` or `dig https://example.com`.
- `grep -iE 'a|b'` — flags stick to the dash. `grep - iE` makes grep treat `iE` as a file name.

## Filtering `whois`

```bash
whois 203.0.113.10 | grep -iE 'orgname|netname|owner'
```

- `|` sends the output of `whois` into `grep`
- `-i` ignores case (`OrgName` = `orgname`)
- `-E` enables extended regex, so `|` inside quotes means "or"

## Key lesson: CDN edge vs origin

A name proxied by a CDN (e.g. Cloudflare) resolves to the **CDN's IPs**, and
`whois` shows the CDN as owner. A name that is *not* proxied resolves straight to
the **origin server**, and `whois` shows the hosting provider.

One unproxied subdomain referenced in a site's JavaScript is enough to reveal the
origin IP: **DevTools → find API host in JS → `dig` → `whois`**. That is passive
recon: no attack, only public information chained together.

## Finding the origin behind a CDN (and why the proxy alone is not enough)

If every hostname is proxied, `dig` only returns CDN IPs. Attackers still find
the origin through:

| Technique | How it works | What actually protects |
|---|---|---|
| Forgotten subdomain | List subdomains from certificate logs (`crt.sh`, query `%.example.com`), then `dig` each one | Proxy **every** record that points to the origin |
| DNS history | Services like SecurityTrails keep the IP a name had *before* it was proxied | Origin firewall (the old IP is still the same server) |
| Internet-wide scanning | Censys / Shodan connect to every IPv4 and record the TLS certificate; an origin serving `example.com`'s cert gets linked to the name | Origin firewall allowing only the CDN's IP ranges |
| Email headers | Mail sent directly from the server carries its IP | Send mail through an external provider |
| MX / TXT (SPF) records | Sometimes list the origin IP | Review those records |

**Takeaway:** hiding the IP is not a security control once it has leaked.
The control is the firewall: the attacker may know the IP, but the server only
answers the CDN.
