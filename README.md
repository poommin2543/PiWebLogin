# Raspberry Pi Web Login Guide

## 📖 Overview
This guide explains how to log in to a Raspberry Pi from a web browser on another device. Typically, SSH is used, but these methods allow access via a web interface.

---

## 🖥️ 1. Web-based SSH (Shellinabox)
### Install Shellinabox
```sh
sudo apt update
sudo apt install shellinabox -y
```
### Configure Shellinabox
```sh
sudo nano /etc/default/shellinabox
```
Modify the line:
```sh
SHELLINABOX_ARGS="--no-beep --disable-ssl"
```
### Start and Enable Service
```sh
sudo systemctl enable shellinabox
sudo systemctl start shellinabox
```
### Access via Web Browser
- Open: `http://<RaspberryPi-IP>:4200`
- Log in using your Raspberry Pi credentials

---

## 🔐 2. Secure Web Access with Cloudflare Tunnel
### Install Cloudflare Tunnel
```sh
lsb_release -cs  # Check OS codename (e.g., bookworm, bullseye, focal, jammy)
```
```sh
echo "deb [signed-by=/usr/share/keyrings/cloudflare-main.gpg] https://pkg.cloudflare.com/cloudflared bookworm main" | tee /etc/apt/sources.list.d/cloudflared.list
apt update && apt install cloudflared
```
### Authenticate and Create a Tunnel
```sh
cloudflared tunnel login
cloudflared tunnel create <NAME>
```
### Verify Tunnel
```sh
cloudflared tunnel list
```
### Configure Cloudflare Tunnel
Navigate to the Cloudflare directory:
```sh
cd .cloudflared/
nano config.yml
```
Add the following configuration:
```yaml
tunnel: uuid
credentials-file: /root/.cloudflared/uuid.json

ingress:
  - hostname: domain.com
    service: http://localhost:4200
    originRequest:
      noTLSVerify: true
  - service: http_status:404
```
### Route DNS to Cloudflare Tunnel
```sh
cloudflared tunnel route dns <NAME> domain.com
```
### Run the Tunnel
```sh
cloudflared tunnel run <NAME>
```

---

## 📊 Summary
| Method | Description | Access URL |
|--------|-------------|-------------|
| **Shellinabox** | Web-based SSH terminal | `http://<RaspberryPi-IP>:4200` |
| **Cloudflare Tunnel** | Secure remote SSH access via Cloudflare | `https://domain.com` |

For advanced configurations, refer to the respective documentation of each tool.

---

### 📝 Notes
- Ensure your Raspberry Pi is connected to the network and has a **static IP**.
- For security, consider setting up **firewall rules** and **SSL encryption**.
- Use **fail2ban** or similar tools to prevent unauthorized access.

🚀 Happy hacking with Raspberry Pi! 💡🎉
