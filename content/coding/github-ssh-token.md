+++
title = 'How to open an account'
date = 2026-02-06
tags = ['Ubuntu']
type = 'posts'
+++

# Connecting to GitHub from the Command Line

## Option 1: SSH Key

### Step 1: Generate an SSH Key
```bash
ssh-keygen -t ed25519 -C "your_email@example.com" -f ~/.ssh/github
```
Press Enter to accept the default file location, then set a passphrase (optional).

### Step 2: Add Your Key to the SSH Agent
```bash
eval "$(ssh-agent -s)"
ssh-add ~/.ssh/github
```

### Step 3: Configure SSH for GitHub
Add the following to `~/.ssh/config` (create the file if it doesn't exist):
```
Host github.com
  HostName github.com
  User git
  IdentityFile ~/.ssh/github
  AddKeysToAgent yes
  UseKeychain yes
```
> Remove `UseKeychain yes` if you are on Linux.

This tells SSH which key to use for GitHub and handles the agent automatically —
you won't need to run `ssh-add` manually again.

### Step 4: Copy Your Public Key
```bash
cat ~/.ssh/github.pub
```
Copy the entire output.

### Step 5: Add the Key to GitHub
1. Go to **GitHub → Settings → SSH and GPG keys**
2. Click **New SSH key**
3. Give it a meaningful name like `"server-name"` so you can identify it later
4. Paste your public key and save

### Step 6: Test the Connection
```bash
ssh -T git@github.com
```
You should see: `Hi username! You've successfully authenticated.`

### Step 7: Clone Using SSH
```bash
git clone git@github.com:username/repository.git
```

---

## Option 2: Personal Access Token (HTTPS)

### Step 1: Generate a Token on GitHub
1. Go to **GitHub → Settings → Developer settings → Personal access tokens → Tokens (classic)**
2. Click **Generate new token**
3. Give it a meaningful name like `"server-name"` so you know where it's used
4. Select scopes (at minimum: `repo`)
5. Click **Generate token** and copy it — you won't see it again

### Step 2: Clone Using HTTPS
```bash
git clone https://github.com/username/repository.git
```
When prompted, enter:
- **Username:** your GitHub username
- **Password:** your personal access token (not your GitHub password)

### Step 3: Credential Storage
On **macOS and Windows**, Git automatically saves your token in the system
keychain after the first login — you won't be prompted again.

On **Linux**, credentials are not stored by default. To enable caching:
```bash
# Store permanently on disk
git config --global credential.helper store

# Or cache in memory for 1 hour
git config --global credential.helper 'cache --timeout=3600'
```

---

## Quick Comparison

| | SSH Key | Personal Access Token |
|---|---|---|
| **Security** | Very high | High |
| **Setup effort** | Medium | Low |
| **Expires** | No | Optional |
| **Best for** | Regular use | CI/CD, scripting |