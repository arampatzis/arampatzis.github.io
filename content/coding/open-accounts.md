+++
title = 'How to open an account'
date = 2026-02-06
tags = ['Ubuntu']
type = 'posts'
+++


### Account Setup

1.  Send me your preferred username: `{USERNAME}`
2.  Choose a name for the server: `{HOSTNAME}`
2.  Open a terminal and run the following command to generate your key pair
(press Enter when prompted to leave the passphrase empty):
    ```bash
    ssh-keygen -t ed25519 -C "your-email" -f ~/.ssh/{HOSTNAME}
    ```
3.  Send me the contents of the public key (run this and copy the output):
    ```bash
    cat ~/.ssh/{HOSTNAME}.pub
    ```
    Never send the contents of the private key!

---

### How to Connect

You will receive an email with the IP address of the server: `{IP_ADDRESS}` 

**Option 1: Direct Command**
```bash
ssh -i ~/.ssh/{HOSTNAME} {USERNAME}@{IP_ADDRESS}
```

**Option 2: SSH Config (Recommended)**
1.  Add the following to your `~/.ssh/config` file:
    ```ssh
    Host {HOSTNAME}
        HostName {IP_ADDRESS}
        User {USERNAME}
        IdentityFile ~/.ssh/{HOSTNAME}
        IdentitiesOnly yes
    ```
2.  Connect with:
    ```bash
    ssh {HOSTNAME}
    ```
