
# Opening a Reverse SSH Tunnel from Remote Host to Local Host

```bash
transfer start
```


Opens a **reverse SSH tunnel** from the Campus Cluster login node back to port 22 on your Mac:


```bash
ssh -N -o ExitOnForwardFailure=yes \
     -R 8022:localhost:22 gclopton@cc-login1.campuscluster.illinois.edu
```


# The `transfer` command


**Syntax:**

```
transfer <source> to <target> [options]
```


**Minimal:**

```bash
transfer results.csv to cc
```



### Transfer.sh


```bash title:"local host transfer.sh"
#!/usr/bin/env bash
# transfer — push a file/dir from your Mac to an HPC host
#            OR open a reverse SSH tunnel so the host can push back.
#
# Usage:
#   transfer <src> to <cc|bridges> [-d] [-n NAME]
#   transfer start <cc|bridges>          # open reverse tunnel
#
# Options:
#   -d | --date            Prepend mm-dd-yy-HHMM folder on remote host.
#   -n | --name <name>     Wrap payload in a custom-named folder.
# ----------------------------------------------------------------------------
set -euo pipefail

# ───────────────────── reverse-tunnel mode ────────────────────────────────
if [[ ${1:-} == start ]]; then
  target=${2:-cc} # default to cc if omitted
  case "$target" in
  cc)
    echo "⏳  Opening reverse tunnel to cc-login1…"
    exec ssh -N -o ExitOnForwardFailure=yes \
      -R 8022:localhost:22 gclopton@cc-login1.campuscluster.illinois.edu
    ;;
  bridges)
    echo "⏳  Opening reverse tunnel to bridges2-login…"
    exec ssh -N -o ExitOnForwardFailure=yes \
      -R 8023:localhost:22 clopton@bridges2.psc.edu
    ;;
  *)
    echo "Unknown target '$target' (use cc | bridges)"
    exit 1
    ;;
  esac
fi

# ───────────────────── regular push mode ──────────────────────────────────
DATE_FLAG=0
CUSTOM=""

usage() {
  cat <<EOF
Usage:
  transfer <src> to <cc|bridges> [-d] [-n NAME]
  transfer start <cc|bridges>

Options:
  -d, --date      Prepend date-stamp folder (mm-dd-yy-HHMM) on remote
  -n, --name NAME Wrap payload in custom-named folder on remote
EOF
  exit 1
}

[[ $# -ge 3 ]] || usage
SRC=$1
shift
[[ $1 == to ]] || usage
shift
TARGET=$1
shift

while [[ $# -gt 0 ]]; do
  case $1 in
  -d | --date) DATE_FLAG=1 ;;
  -n | --name)
    shift
    [[ $# -ge 1 ]] || usage
    CUSTOM=$1
    ;;
  -h | --help) usage ;;
  *)
    echo "Unknown option $1"
    usage
    ;;
  esac
  shift
done

[[ -e $SRC ]] || {
  echo "❌ source '$SRC' does not exist"
  exit 1
}

# ─────────────── host map (no associative arrays needed) ──────────────────
case "$TARGET" in
cc)
  REMOTE_USER="gclopton"
  REMOTE_HOST="cc-login.campuscluster.illinois.edu"
  DEST_BASE="/u/gclopton/transfer"
  ;;
bridges)
  REMOTE_USER="clopton"
  REMOTE_HOST="bridges2.psc.edu"
  DEST_BASE="/ocean/projects/mat220011p/clopton/transfer"
  ;;
*)
  echo "❌ unknown target '$TARGET'"
  usage
  ;;
esac

# ─────────────── build destination path on remote host ────────────────────
DEST_PATH="$DEST_BASE"
[[ $DATE_FLAG -eq 1 ]] && DEST_PATH+="/$(date +%m-%d-%y-%H%M)"
[[ -n $CUSTOM ]] && DEST_PATH+="/$CUSTOM"

# ─────────────── create directory & copy payload ──────────────────────────
ssh "${REMOTE_USER}@${REMOTE_HOST}" "mkdir -p '$DEST_PATH'"

if [[ -d $SRC ]]; then
  # recursive copy for directories
  scp -O -r "$SRC" "${REMOTE_USER}@${REMOTE_HOST}":"$DEST_PATH/"
else
  scp -O "$SRC" "${REMOTE_USER}@${REMOTE_HOST}":"$DEST_PATH/"
fi

echo "✅  $SRC → $REMOTE_HOST:$DEST_PATH"


```


```bash title:"cc transfer.sh"

```


# Transferring from Campus Cluster to 
### Recommended simplest path

1. **On you Mac** – open one tunnel and keep it open:
    

    
    `transfer start cc     # leaves you logged into cc-login1`
    
2. **On Campus Cluster** – Make sure you are on login node 1

    
    `ssh cc-login1         # start a session on the same node transfer -d NPAR1`
    

No script edits needed; just align the login node with the tunnel.



# Setting up key pair for bridges


```bash
[clopton@bridges2-login011 transfer]$ ssh-keygen -t ed25519 -C "bridges-to-mac"



Generating public/private ed25519 key pair.
Enter file in which to save the key (/jet/home/clopton/.ssh/id_ed25519):
Enter passphrase (empty for no passphrase):
Enter same passphrase again:
Your identification has been saved in /jet/home/clopton/.ssh/id_ed25519.
Your public key has been saved in /jet/home/clopton/.ssh/id_ed25519.pub.
The key fingerprint is:
SHA256:3cN5UDoBQWvh46RJva/913t/dBw95OupVuVGmGfDFfc bridges-to-mac
The key's randomart image is:
+--[ED25519 256]--+
|         .=o. ..o|
|         o o + .+|
|        . B + +oE|
|       . B = +o**|
|        S + = .**|
|           . o o*|
|            . oo+|
|           o . o=|
|          . oooo*|
+----[SHA256]-----+



[clopton@bridges2-login011 transfer]$ ssh-copy-id -p 8023 -i ~/.ssh/id_ed25519.pub gradyclopton@localhost


/usr/bin/ssh-copy-id: INFO: Source of key(s) to be installed: "/jet/home/clopton/.ssh/id_ed25519.pub"
/usr/bin/ssh-copy-id: INFO: attempting to log in with the new key(s), to filter out any that are already installed
/usr/bin/ssh-copy-id: INFO: 1 key(s) remain to be installed -- if you are prompted now it is to install the new keys
Password:

Number of key(s) added: 1

Now try logging into the machine, with:   "ssh -p '8023' 'gradyclopton@localhost'"
and check to make sure that only the key(s) you wanted were added.

[clopton@bridges2-login011 transfer]$
```