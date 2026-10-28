

## 2  Installation

```bash
# 1  Install script
mkdir -p ~/utils/.nav
cp nav.py ~/utils/.nav/
chmod +x ~/utils/.nav/nav.py

# 2  Add wrapper to ~/.zshrc  (adapt to ~/.bashrc on clusters)
function nav() {
  # top‑level commands that need no cd
  if [[ "$1" == create || "$1" == list || "$1" == alphabetize \
        || "$1" == search || "$1" == open || "$1" == copy \
        || "$1" == help || "$1" == -h || "$1" == --help ]]; then
    python3 ~/utils/.nav/nav.py "$@"; return
  fi
  # alias‑scoped but stay‑put commands
  if [[ "$2" == show || "$2" == change || "$2" == del || "$2" == tag ]]; then
    python3 ~/utils/.nav/nav.py "$@"; return
  fi
  # plain alias → cd
  dest=$(python3 ~/utils/.nav/nav.py "$@")
  [[ -d $dest ]] && cd "$dest" || echo "No such directory: $dest"
}

source ~/.zshrc  # reload shell once
```

On HPC clusters that default to *bash*, translate the wrapper accordingly.
