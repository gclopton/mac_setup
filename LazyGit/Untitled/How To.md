


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

#### Troubleshooting tips

- If `ssh -T git@github.com` does **not** greet you, your key is missing from GitHub or the remote URL is using HTTPS.
    
- If you see _“fatal: Authentication failed for https://…”_, switch the remote to SSH:
    
    ```bash
    git remote set-url origin git@github.com:<you>/<repo>.git
    ```
    
- If lazygit complains about “no upstream”, just push with `P` and accept the prompt to create and track the branch.
    

With these steps you can recreate the entire setup from scratch any time, entirely in full sentences—and that should keep it stuck in long-term memory. Cool beans!