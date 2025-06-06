




If you accidentally set the wrong url, you can change it with this command
```
git remote set-url origin git@github.com:gclopton/test_vault.git
```




make sure it is set correctly

```
❯ git remote -v
```



# Set Your SSH key

check
```
ssh -T github.com
```


Do you already have an SSH keypair?

```
ls -al ~/.ssh | grep -E '\.pub$'
```



[Installing LazyGit and Setting up a Git Repo](https://chatgpt.com/c/6838bb97-8ee0-800d-a07b-c604bbc4e163)


## Point repository towards ssh key:

```
git remote set-url origin git@github.com:gclopton/<remote_repository_name>.git
```


**Example:**
```
git remote set-url origin git@github.com:gclopton/vasp.git
```


git remote set-url origin git@github.com:gclopton/vasp.git
