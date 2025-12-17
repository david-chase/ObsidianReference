#windows #ssh #github

This document summarizes how to enable and use SSH authentication on **Windows 11** for GitHub access.

---

## 1. Enable the OpenSSH Client (and Agent)

Windows splits OpenSSH into two components:
- **Client** – required to run `ssh`
- **Agent** – required to store and present keys to GitHub

### Check installation status

Open **PowerShell (Administrator)** and run:

```powershell
Get-WindowsCapability -Online | Where-Object Name -like 'OpenSSH*'
```

You should see:

```
OpenSSH.Client~~~~0.0.1.0     Installed
OpenSSH.Agent~~~~0.0.1.0      Installed
```

### Install missing components

```powershell
Add-WindowsCapability -Online -Name OpenSSH.Client~~~~0.0.1.0
Add-WindowsCapability -Online -Name OpenSSH.Agent~~~~0.0.1.0
```

Reboot after installation.

### Enable and start the SSH agent

```powershell
Set-Service ssh-agent -StartupType Automatic
Start-Service ssh-agent
```

Verify:

```powershell
Get-Service ssh-agent
```

Status should be **Running**.

---

## 2. Create an SSH Key

In **regular (non-admin) PowerShell**:

```powershell
ssh-keygen -t ed25519 -C "your_email@example.com"
```

- Press **Enter** to accept the default location
- Optionally set a passphrase

This creates:

```
~\.ssh\id_ed25519
~\.ssh\id_ed25519.pub
```

---

## 3. Load the Key Into the Agent

```powershell
ssh-add $env:USERPROFILE\.ssh\id_ed25519
```

Confirm:

```powershell
ssh-add -l
```

Your key fingerprint should be listed.

---

## 4. Add the Key to GitHub

Copy the public key:

```powershell
Get-Content $env:USERPROFILE\.ssh\id_ed25519.pub
```

Then in GitHub:
1. Go to **Settings → SSH and GPG keys**
2. Click **New SSH key**
3. Title: `Windows 11 – <machine-name>`
4. Paste the public key
5. Save

---

## 5. Test Authentication

```powershell
ssh -T git@github.com
```

Success message:

```
Hi <username>! You've successfully authenticated, but GitHub does not provide shell access.
```

---

## 6. Verify Git Repository Uses SSH

```powershell
git remote -v
```

Expected format:

```
git@github.com:owner/repo.git
```

If needed:

```powershell
git remote set-url origin git@github.com:owner/repo.git
```

---

## 7. Result

Git operations (`pull`, `push`) now work without passwords or tokens using SSH authentication.
