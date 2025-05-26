# Tailscale Remote Access for HPC and Lab Servers

A lightweight, stable, and secure replacement for VS Code Tunnels using [Tailscale](https://tailscale.com) to enable SSH access to internal servers from anywhere — without requiring a VPN.

---

## Use Case

For researchers and students working with **HPC clusters or lab servers** that:

- **Can be accessed only from inside a university network**
- **Require VPN login which conflicts with global proxy/VPN**
- **Don’t expose SSH to public IPs for security reasons**

This setup uses **Tailscale + SSH** to create a _persistent, secure private network_ so you can SSH into your servers **from anywhere**, bypassing VPN issues entirely.

---

## Why Not VS Code Tunnels?

While VS Code's Remote Tunnels are great, they have **major drawbacks** in HPC and academic settings:

| Issue                     | VS Code Tunnels             | Tailscale Alternative          |
|--------------------------|-----------------------------|--------------------------------|
| Stability                | ❌ Tunnels frequently drop   | ✅ Runs 24/7 reliably           |
| SSH Support              | ❌ Hidden behind VS Code     | ✅ Native SSH works anywhere    |
| Public IP Required       | ❌ VS Code cloud routing     | ✅ Tailscale uses NAT traversal |
| VPN Compatibility        | ❌ Conflicts with VPNs       | ✅ No VPN needed                |
| Onboarding for Teammates | ❌ VS Code account required  | ✅ SSH + Tailscale = simple     |

---

## What You'll Get

- Native SSH access to your lab/server via private IP (e.g., `100.x.x.x`)
- Persistent secure connection — even without public IP or port forwarding
- Optional `ssh` alias (e.g., `ssh mylab`) for one-liner logins
- Works across Linux, macOS, Windows

---

## Setup Steps

### 1. Install Tailscale on **both your laptop** and the **remote server**

#### On the server (e.g., Ubuntu):

Install with one command:

```bash
curl -fsSL https://tailscale.com/install.sh | sh
```

Manually install:

```bash
curl -fsSL https://pkgs.tailscale.com/stable/ubuntu/noble.noarmor.gpg | sudo tee /usr/share/keyrings/tailscale-archive-keyring.gpg >/dev/null
curl -fsSL https://pkgs.tailscale.com/stable/ubuntu/noble.tailscale-keyring.list | sudo tee /etc/apt/sources.list.d/tailscale.list
sudo apt update
sudo apt install tailscale
sudo tailscale up
tailscale ip -4
```

#### On your laptop (macOS or Windows):

- Download from [tailscale.com/download](https://tailscale.com/download)
- Login with GitHub/Google
- Done

Both devices will appear in the same "Tailnet".

---

### 2. Find the server's Tailscale IP

On the laptop, run:
```bash
tailscale status
```

Find the entry for your server — e.g.:
```
100.101.102.103  my-hpc-server
```

---

### 3. SSH into the server

```bash
ssh your_username@100.101.102.103
```

No VPN. No tunnels. No pain.

---

## Optional: Configure SSH Key Authentication

To avoid entering your password every time:

On your **laptop**:
```bash
ssh-keygen -t ed25519 -C "tailscale"
ssh-copy-id your_username@100.101.102.103
```

Test it:
```bash
ssh your_username@100.101.102.103  # No password prompt!
```

---

## Pro Tips

- Add `~/.ssh/config` alias:
  ```ini
  Host mylab
      HostName 100.101.102.103
      User your_username
      IdentityFile ~/.ssh/id_ed25519
  ```
  Then use:
  ```bash
  ssh mylab
  ```

- Enable Tailscale on boot (`tailscale up` auto-reconnects)

- Use `tailscale status` to view all your connected nodes

---
## FAQ

### Will this expose my server to the internet?

> No. Tailscale creates a **private encrypted mesh network**. Your server is only reachable by **your own devices**.

### Does this replace my school VPN?

> Yes, for internal SSH access. You can now access your lab server without logging into your school VPN.

### Can I use this with Windows servers or WSL?

> Yes. Tailscale supports Windows and WSL2, with full SSH support.

---

## Maintained by

Created and maintained by [@zedong-peng](https://github.com/zedong-peng)  
Feel free to open issues, contribute improvements, or share this with your labmates and team!