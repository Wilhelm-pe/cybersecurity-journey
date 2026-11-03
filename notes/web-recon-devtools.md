# Web recon with browser DevTools (F12)

Everything the browser downloads, an attacker can read.

| Tab | What to look for |
|---|---|
| Elements | HTML comments, hidden inputs, links to admin areas |
| Sources | JS bundles: API hosts, hardcoded keys, route names, `.map` source maps |
| Network | Every request: which host it goes to, response headers (`server`, `x-powered-by`), API responses |
| Application | Cookies, localStorage / sessionStorage tokens |
| Console | Leftover debug messages and errors |

**Ctrl+Shift+F** in Sources searches all loaded files: try `api`, `key`,
`token`, `secret`, `http`, `password`.

## Quick checks by URL

- `/robots.txt` and `/sitemap.xml` — a map of what the owner wants hidden from search engines
- `/.git/HEAD`, `/.env` — must never be served
- `*.js.map` — source maps should not be public in production

## Security headers worth checking

`strict-transport-security`, `content-security-policy`, `x-frame-options`
(or CSP `frame-ancestors`), `x-content-type-options`, `referrer-policy`.

```bash
curl -sI https://example.com | grep -iE 'strict|content-security|x-frame|x-content|referrer'
```

## False positives in directory brute forcing

Single-page apps answer **200 with the same page for any path**, so tools like
`dirb` "find" everything. CDNs may also return short error pages (e.g. Cloudflare
520/522, ~16 bytes) under load, which tools count as hits.

Fix: compare response sizes and filter the default one.

```bash
gobuster dir -u http://localhost:3000 -w /usr/share/wordlists/dirb/common.txt --exclude-length <default-size>
```
