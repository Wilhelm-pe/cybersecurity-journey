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
| 0 → 1 | `ls`, `cat`; read a file in the home directory |
| 1 → 2 | file named `-`; `cat ./-` (a leading `-` would look like a command option) |
| 2 → 3 | filename with spaces; quote or escape it: `cat "spaces in this filename"` |
| 3 → 4 | hidden file in a folder; `ls -a`, then `cat` the dotfile |
| 4 → 5 | only one human-readable file among many; `file ./*` tells text from binary |
| 5 → 6 | find by properties: `find . -size 1033c ! -executable`. In `1033c` the `c` means **bytes**; without it, `find` counts 512-byte blocks |
| 6 → 7 | search the whole system by owner/group/size: `find / -user bandit7 -group bandit6 -size 33c 2>/dev/null` (`2>/dev/null` hides permission-denied noise) |
