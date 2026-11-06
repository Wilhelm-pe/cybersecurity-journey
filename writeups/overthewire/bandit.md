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
| 7 → 8 | huge file, one useful line: `grep millionth data.txt` prints only the lines that contain the word |
| 8 → 9 | the only line that appears once: `sort data.txt \| uniq -u`. `uniq` only compares **neighbouring** lines, so `sort` must come first |
| 9 → 10 | readable text hidden in a binary file: `strings data.txt \| grep "=="` (`strings` keeps only printable sequences) |
| 10 → 11 | Base64 is an **encoding, not encryption**: anyone can reverse it with `base64 -d data.txt`. Commands read a file directly, no need to copy and paste |

## Extra: what if the file has more than Base64?

`base64 -d` does **not** find the Base64 part; it tries to decode the whole input. With normal text mixed in:

```bash
base64 -d mixed.txt        # error: invalid input (commas, spaces, "!" are not in the alphabet)
base64 -d -i mixed.txt     # still fails: ordinary letters ARE valid Base64 characters, so the text gets glued in
grep -oE '^[A-Za-z0-9+/]{16,}={0,2}$' mixed.txt | base64 -d   # works: filter first, then decode
```

- `[A-Za-z0-9+/]` is the 64-character alphabet; `{16,}` skips short words; `={0,2}` allows the padding; `^...$` means the whole line.
- Pattern: **filter first, transform after** (same idea as `strings | grep` in level 9).
- Looking like Base64 is not proof: a long plain word also matches. Use context (length, `=` padding, where it appeared).

## Takeaways so far

- Pipes (`|`) chain small tools: each one does one job (`sort`, `uniq`, `grep`, `strings`).
- Read the error and the man page (`man uniq`) before guessing.
- Encoding (Base64) hides nothing; real secrecy needs encryption with a key.
