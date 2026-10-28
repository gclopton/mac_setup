

## What is *nav*?

* **Single‑file** Python 3 script + tiny shell wrapper.
* Stores aliases in **`~/utils/.nav/aliases.psv`** (pipe‑separated).
* Jump (`cd`), **search**, **copy**, **open in GUI**, **tags**, duplicate checks, auto‑mkdir.






## 5  Everyday snippets

```bash
# Save current directory as an alias
nav create "$(pwd)" here

# Quick jump
nav here

# Copy its path to clipboard
nav copy here       # "📋  copied to clipboard"

# GUI open (ignored on headless servers)
nav open here

# Tag & search
nav here tag add project
nav search project
```

Optional helper to copy *current* directory instantly (add to shell rc):

```zsh
alias cwdcopy='pwd | pbcopy'          # macOS
# alias cwdcopy='pwd | xclip -sel clip'  # Linux/X11
```

---

## 6  How it works

1. Wrapper parses the command line to decide whether to `cd`.
2. Python script loads/saves `aliases.psv`, handles features.
3. Clipboard uses `pbcopy`, `xclip`, or `wl-copy`; falls back to echo.

---

## 7  FAQ

| Issue                                             | Remedy                                                           |
| ------------------------------------------------- | ---------------------------------------------------------------- |
| `Unknown alias.`                                  | Check spelling or `nav search <txt>`.                            |
| Clipboard copy prints path instead of silent copy | Install `pbcopy` (macOS), `xclip` (X11), or `wl-copy` (Wayland). |
| `open` says no command                            | Cluster/headless session—feature skipped.                        |
| Need same aliases on another machine              | `scp ~/utils/.nav/aliases.psv` to the other box or sync via git. |



## 8  Roadmap ideas

* Fuzzy‑frecency ranking (`nav top`).
* Shell tab‑completion for aliases.
* Export/import helpers.
  PRs welcome!



# bashrc


```bash title:".bashrc"
# ─── nav wrapper ────────────────────────────────────────────────────────────
nav () {
    # Run nav.py and capture its output (suppress trailing newline).
    local out
    out=$(python3 ~/utils/.nav/nav.py "$@")

    local status=$?            # remember the program’s exit status

    # If nav.py succeeded *and* its output is a directory, cd there.
    if [[ $status -eq 0 && -d $out ]]; then
        cd "$out"
    else
        # For sub-commands like “nav <alias> show” just echo what nav.py printed.
        printf '%s\n' "$out"
        return $status
    fi
}
# ────────────────────────────────────────────────────────────────────────────
```

