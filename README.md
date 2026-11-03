# Cybersecurity Journey

Hands-on log of my path into cybersecurity: concepts, labs and write-ups,
written as I learn them.

I build and run web systems for small businesses (Linux VPS, Docker, Traefik,
Cloudflare). This repository documents how I am learning to attack and defend
that kind of environment, the right way: **only on systems I own or am
authorized to test, and on legal training platforms.**

## Current focus

| Phase | Topics | Status |
|---|---|---|
| 0. Fundamentals | Networking, DNS, HTTP, Linux CLI, Windows, Python basics | 🟡 in progress |
| 1. Offensive & defensive basics | Recon, Nmap, OWASP Top 10, logs, firewalls | ⚪ next |
| 2. Entry certification | eJPT (practical) | ⚪ planned |

Platforms: TryHackMe (Pre Security path), OverTheWire (Bandit), OWASP Juice Shop (local lab).

## Repository map

| Folder | What is inside |
|---|---|
| [`notes/`](notes/) | Short concept notes: the question each tool answers and why |
| [`writeups/`](writeups/) | Reasoning behind solved rooms and levels (no flags, no passwords) |
| [`labs/`](labs/) | How my home lab is built and what I tested on it |

## Rules I follow

1. Test only what I own or have written permission to test.
2. No flags, passwords or spoilers from training platforms.
3. No real hostnames, IPs or open findings from production systems.
   Real cases are published only after they are fixed, and anonymized.

## Log

| Date | What I did |
|---|---|
| 2026-11-03 | Installed Kali Linux in VirtualBox (manual partitioning, GRUB). Started OWASP Juice Shop in Docker as a local target. |
| 2026-11-03 | Passive recon of my own infrastructure: DevTools, `dig`, `whois`, `nmap`. Learned to tell a CDN edge from the origin server and to read false positives in directory brute forcing. |
| 2026-11-03 | Studied how origin IPs leak behind a CDN (forgotten subdomains, certificate transparency, DNS history, Censys/Shodan) and why an origin firewall matters more than hiding the IP. |
