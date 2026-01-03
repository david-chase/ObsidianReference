#github #ssh #linux #windows

## Windows

### Generate an SSH key (Ed25519 – recommended)

``` powershell
ssh-keygen -t ed25519 -C "your_email@example.com"
```


When prompted:

- **File location** → press **Enter** (default is best)
- **Passphrase** → optional but recommended

This creates:

- Private key: `~\.ssh\id_ed25519`
- Public key: `~\.ssh\id_ed25519.pub`

---
### Start the SSH agent and load your key

In a PowerShell Admin window:

``` powershell
Set-Service ssh-agent -StartupType Automatic
Start-Service ssh-agent
```

``` powershell
Start-Service ssh-agent
ssh-add $env:USERPROFILE\.ssh\id_ed25519

# Confirm:
ssh-add -l
```

---
### Add the public key to GitHub

Copy the key:

``` powershell
Get-Content $env:USERPROFILE\.ssh\id_ed25519.pub | Set-Clipboard
```

Then in GitHub:

1. Go to **Settings → SSH and GPG keys**
2. Click **New SSH key**
3. Paste the key
4. Type: **Authentication key**
5. Save

---
## 7. Use SSH URLs for repositories

Clone using SSH (not HTTPS):

``` powershell
git clone git@github.com:OWNER/REPO.git
```

If you already cloned via HTTPS:

``` powershell
git remote set-url origin git@github.com:OWNER/REPO.git
```


# Linux

## 1. Check for Existing SSH Keys

```bash
ls -al ~/.ssh
```

Look for files like `id_rsa`, `id_rsa.pub`, `id_ed25519`, or `id_ed25519.pub`.

---

## 2. Generate a New SSH Key (If Needed)

```bash
ssh-keygen -t ed25519 -C "your_email@example.com"
```

For RSA:
```bash
ssh-keygen -t rsa -b 4096 -C "your_email@example.com"
```

---

## 3. Add SSH Key to SSH Agent

```bash
eval "$(ssh-agent -s)"
ssh-add ~/.ssh/id_ed25519
```

---

## 4. Add SSH Key to GitHub

Copy key to clipboard:

```bash
cat ~/.ssh/id_ed25519.pub | xclip -selection clipboard
```

Go to: https://github.com/settings/ssh/new  
Paste the key, name it, and save.

---

## 5. Test the SSH Connection

```bash
ssh -T git@github.com
```

Expected output:
```
Hi yourusername! You've successfully authenticated, but GitHub does not provide shell access.
```

---

## 6. Switch an Existing Repo to SSH

```bash
git remote set-url origin git@github.com:yourusername/yourrepo.git
```

---

## Cloning via HTTPS (Alternative)

```bash
git clone https://github.com/username/repository.git
```

- Public repos require no login.
- Private repos require GitHub username + personal access token (PAT).

Cache credentials:
```bash
git config --global credential.helper cache
```

Or store them:
```bash
git config --global credential.helper store
```
