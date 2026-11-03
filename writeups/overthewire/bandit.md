# OverTheWire — Bandit

What each level teaches. **No passwords here**, per OverTheWire's request.

## Connecting

```bash
ssh bandit0@bandit.labs.overthewire.org -p 2220
```

- Port **2220**, not 22.
- Some networks (corporate/government) block outbound SSH; a timeout is a network block, not a wrong command.
- To move to the next level, open a new SSH session as the next user (`bandit1@...`) with the password you found.

## Levels

| Level | Concept |
|---|---|
| 0 → 1 | `ls`, `cat`; reading a file in the home directory |
