# Home lab

Isolated practice environment on my own computer.

## Setup

| Piece | Details |
|---|---|
| Hypervisor | Oracle VirtualBox on Windows |
| Attacker VM | Kali Linux, installed from ISO (2 vCPU, 4 GB RAM, ~27 GB disk) |
| Target | OWASP Juice Shop in Docker inside the Kali VM (`localhost:3000`) |

## Kali install notes

- The Debian installer "main menu" is not an error: it appears after `Esc` / Go Back, or when a step fails.
- Partitioning: *Guided – use entire disk* → *All files in one partition* → ext4 `/` + swap. The disk is the VM's virtual disk; the host is untouched.
- Install GRUB to `/dev/sda`. Remove the ISO from the virtual drive after install.

## Juice Shop

```bash
sudo apt install -y docker.io
sudo docker run -d -p 3000:3000 bkimminich/juice-shop
```

First challenge: find the hidden score board by reading the JS bundle in DevTools.

## Exercises done

- [x] Kali installed
- [x] Juice Shop running locally
- [ ] Directory brute force with `gobuster`, filtering the SPA default response
- [ ] Find the score board via DevTools
