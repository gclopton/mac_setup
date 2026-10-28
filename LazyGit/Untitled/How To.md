


## How to initialize a brand-new project and push it to GitHub with lazygit (or plain Git)

1. **Create the empty repository on GitHub first.**  
    Sign in to GitHub, click **➕ ➜ New repository**, give it a name (e.g. `vasp`), keep it _empty_ (no README, licence, or .gitignore yet), and click **Create repository**. GitHub immediately shows you both an HTTPS URL (`https://github.com/<you>/<repo>.git`) and an SSH URL (`git@github.com:<you>/<repo>.git`).
    
2. **Initialise (or verify) a Git repository in your project folder.**  
    Open a terminal, `cd` into the project directory, and run
    
    ```bash
    git init
    ```
    
    This command creates the hidden `.git` directory that turns a folder into a Git repo.
    
3. **Make sure your computer can authenticate with GitHub via SSH.**
    
    - If `~/.ssh/id_ed25519` already exists and `ssh -T git@github.com` greets you by name, you are done.
        
    - If not, create a key and add the public half to GitHub:
        
        ```bash
        ssh-keygen -t ed25519 -C "my-laptop"
        cat ~/.ssh/id_ed25519.pub          # copy this one-line string
        ```
        
        Go to **Settings ➜ SSH and GPG keys ➜ New SSH key**, paste the key, and save.
        
4. **Point your local repo at the GitHub repo.**  
    Use the **SSH** URL so you never have to type a token:
    
    ```bash
    git remote add origin git@github.com:gclopton/<repo>.git
    ```
    
    (In lazygit you can achieve the same by focusing the _Branches/Remotes_ panel and pressing **`a`** to add a remote.)
    
5. **Stage the files you wish to commit.**  
    In lazygit: focus the _Files_ panel, highlight “/”, and press **A** to stage everything.  
    In plain Git:
    
    ```bash
    git add -A        # stages everything in the working tree
    ```
    
6. **Create the first commit.**
    
    - Lazygit: press **`c`**, type a message such as “Initial commit”, press **Ctrl-s** or **Enter**, then **Esc**.
        
    - CLI:
        
        ```bash
        git commit -m "Initial commit"
        ```
        
7. **Push while _simultaneously_ creating and tracking the remote branch.**  
    Because nothing exists on GitHub yet, you must push with `-u` once:
    
    - Lazygit: press **`P`** (capital P). Lazygit notices there is no upstream and offers to create `origin/master`. Confirm, and it runs `git push -u origin master` for you.
        
    - CLI (equivalent):
        
        ```bash
        git push -u origin master          # or use main instead of master
        ```
        
    
    The `-u` flag tells Git to remember that your local `master` now tracks `origin/master`, so future pushes require only `git push` (or **`P`**).
    
8. **Verify on GitHub.**  
    Refresh the repository page; the commit history should now show your “Initial commit”.
    

---

#### Everyday workflow after the first push

1. Edit or add files.
    
2. **Stage** with ␣ on each file (or **A** for all).
    
3. **Commit** with **`c`** (lazygit) or `git commit -m "...“`.
    
4. **Push** with **`P`** (lazygit) or simple `git push`.
    
5. **Pull** remote changes at any time with **`p`** (lower-case) or `git pull`.
    

---

# Troubleshooting tips

- If `ssh -T git@github.com` does **not** greet you, your key is missing from GitHub or the remote URL is using HTTPS.
    
- If you see _“fatal: Authentication failed for https://…”_, switch the remote to SSH:
    
    ```bash
    git remote set-url origin git@github.com:<you>/<repo>.git
    ```
    
- If lazygit complains about “no upstream”, just push with `P` and accept the prompt to create and track the branch.
    

With these steps you can recreate the entire setup from scratch any time, entirely in full sentences—and that should keep it stuck in long-term memory. Cool beans!

## Unstage and Remove a File from the Commit

```bash
# step back one commit but keep all changes in the working tree
git reset --soft HEAD~1

# stop tracking the credentials file
git rm --cached .obsidian/plugins/smart-connections/data.json

```



**Add the path to `.gitignore`** so it never gets committed again



```bash
echo ".obsidian/plugins/smart-connections/data.json" >> .gitignore
git add .gitignore
```


Amend the existing commit:

```bash
git commit --amend --no-edit
```



Check whether remote is configured:

```bash
git remote -v
```



Push and set the upstream:

```bash
git push --force --set-upstream origin master
```



# Committing and Pushing Edits


Because your branch is already set to track **origin / master**, the workflow is now just a tidy three-step loop:

---

### 1 Stage the change

_In the Files panel_ (blue highlight):

- Move the cursor onto `How To.md`.
    
- Press **Space**.  
    _The file name turns green, showing it’s staged._
    

(If you ever want to stage everything at once, highlight “/” at the top of the tree and press **A**.)

---

### 2 Create a new commit

_Still in the Files panel_:

1. Press **`c`**.
    
2. Lazygit opens its mini-editor. Type a short message such as
    
    ```
    docs: refine setup-how-to
    ```
    
3. Press **Ctrl + s** (or just **Enter**) to save the message, then **Esc** to close the editor.  
    _You’ll now see the new commit appear in the Commits panel._
    

---

### 3 Push the commit

_Any panel is fine_:

- Press **`P`** (capital P).  
    Because the upstream is already configured, lazygit immediately runs `git push` and you’ll see a green ✓ in the command log on success.
    

Refresh the GitHub page and the updated commit will be there.

---

#### Quick mnemonic

> **␣ → c → P**  
> _(Stage → Commit → Push)_

That’s all you need for every future change. Cool beans! 🎉



# Delete a Vault


```bash
sudo rm -rf vasp
```

QAZPLM135pc!
QAZPLM135pc!


# Switching to Remote SSH Before Pushing

What you’re seeing is a classic HTTPS vs SSH (and maybe “wrong-URL”) mix-up:

```
remote: Repository not found.
fatal: Authentication failed for 'https://github.com/gclopton/vasp.git/'
```

**What it means**

1. Your `origin` remote is set to **HTTPS**, so Git prompts for _username / password_.  
    _GitHub stopped accepting account passwords in 2021, so a Personal Access Token (PAT) is now required._
    
2. GitHub then answers **“Repository not found”**, which usually indicates
    
    - the repo was deleted or never created under that URL, **or**
        
    - you’re authenticating anonymously (which is what happens when you hit ⏎ at the prompt).
        

---

## Fix in two minutes

### 1 Double-check the repo actually exists

Open `https://github.com/gclopton/vasp` in your browser.

- **404 page** → create the repo (same name, empty) and you’re good to push.
    
- **Repo exists** → keep going to step 2.
    

### 2 Switch the remote to SSH so you never see the password prompt again

```bash
git remote set-url origin git@github.com:gclopton/vasp.git
```

_(In lazygit: focus the **Branches/Remotes** panel → highlight **origin** → press **e** → paste the SSH URL → Enter.)_

SSH will use the key you already configured earlier (`ssh -T git@github.com` greeted you).

### 3 Push and set the upstream (first time only)

```bash
git push -u origin master      # or main, whichever branch you’re on
```

_(In lazygit: highlight the branch → press **u** → type `origin master` → Enter → **P** to push.)_

After this you can simply press **P** in lazygit (or run plain `git push`) and it will work without prompts.

---

## If you prefer to keep HTTPS

1. **Create a PAT** (GitHub → Settings → Developer settings → Personal access tokens).
    
2. When Git asks for password, paste the PAT.  
    _Username_ is still `gclopton`.
    
3. Optionally store it so you’re not prompted every time:
    
    ```bash
    git config --global credential.helper store
    ```
    

But using **SSH** is less hassle once the key is in place.

---

### Quick checklist for next time

|What to verify|Command|Good output|
|---|---|---|
|Remote URL|`git remote -v`|`origin git@github.com:…`|
|SSH works|`ssh -T git@github.com`|“Hi gclopton!”|
|Upstream set|`git status`|“up to date with ‘origin/master’”|

Once those three line up, the routine is back to ␣ → **c** → **P** every time. Cool beans! 🎉


# Scrubbing a Secret Key and Ammending a Root Commit


**Remove the offending file from the index but keep it on disk**


> [!Question]
> You tried to make an initial commit to your git repository but you obtained an error: 

```bash
GH013: Repository rule violoations found for refs/head/master: Push cannot contain secrets

OpenAI API Key commit: Locations
- commit: 5be78a4d74a4056d5c2ed99caa7bb72b4dd4f8dd 
- path: .obsidian/plugins/smart-connections/data.json:2
```

> [!Question] Cont.
> GitHub has blocked the push. What steps do you need to take to remove or scrub the secret, and then recommit and push again?


**1.) Unstage and remove the file from the commit.**

```bash
git rm --cached .obsidian/plugins/smart-connections/data.json
```


**Add the path to `.gitignore`** **so it never gets committed again**


```bash
echo ".obsidian/plugins/smart-connections/data.json" >> .gitignore
git add .gitignore
```


**Amend the existing commit**

```bash
git commit --amend --no-edit
```



**Force-push (only required because we rewrote history)**
Scenario: Your repo already knows about **`origin`**, it just hasn’t linked (`tracked`) the local **master** branch to that remote yet. Because you rewrote the root commit you also need a **force-push** (`--force` or `-f`).  Do the two things in one command:

```bash
git push --force --set-upstream origin master
```



