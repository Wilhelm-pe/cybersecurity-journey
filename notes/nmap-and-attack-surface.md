# Nmap and attack surface

> Port scanning is **active**. Only scan hosts you own, `localhost`, or
> targets a platform gives you (e.g. TryHackMe over its VPN).

```bash
nmap 203.0.113.10          # top 1000 TCP ports
nmap -sV 203.0.113.10      # also detect service versions
nmap -p 22,80,443 host     # only these ports
```

## Reading the result

- `open`: something is listening and answered.
- `filtered`: no answer, usually a firewall dropping packets.
- `Not shown: 997 filtered`: the firewall is doing its job.

**Scanning a CDN-proxied name scans the CDN, not your server.** Ports like
8080/8443 showing up there belong to the CDN edge. Scan the origin IP to see
your own surface.

## Open ports are not the problem — what is behind them is

A server with zero open ports serves nothing. For each open port, ask:
*what listens here, and what can an attacker try?*

| Port | Purpose | Typical attack | Defense |
|---|---|---|---|
| 22 SSH | Admin access | Password brute force | Key-only auth, `PermitRootLogin no`, fail2ban, optionally VPN-only |
| 80 HTTP | Redirect to HTTPS | — | Redirect everything to 443 |
| 443 HTTPS | Web apps and APIs | Directory brute force, login brute force, app bugs | Auth on every route, rate limiting, strong passwords + 2FA, WAF/CDN, origin firewall allowing only the CDN |

Services bound to `127.0.0.1` (databases, dashboards) are not reachable from
outside at all — check with `ss -tln`.

## Docker gotcha

Ports published by Docker (`-p 443:443`) bypass `ufw` INPUT rules, because Docker
NATs the traffic through the FORWARD chain. Restrictions for container ports go
in the `DOCKER-USER` iptables chain.
