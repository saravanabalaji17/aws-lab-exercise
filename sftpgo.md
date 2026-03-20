



<img width="956" height="505" alt="image" src="https://github.com/user-attachments/assets/83397584-8466-4a07-acc7-0137a970549c" />

<img width="959" height="503" alt="image" src="https://github.com/user-attachments/assets/6a160045-1c48-4a80-95ae-2d1db40c9060" />

<img width="959" height="503" alt="image" src="https://github.com/user-attachments/assets/13c3e926-f7e5-424f-a566-525b1a4d7f22" />

<img width="959" height="506" alt="image" src="https://github.com/user-attachments/assets/8c34b687-adfa-4a1b-ae65-fbad8b43a97b" />



````md
# 🚀 SFTPGo Setup with Nginx & Let's Encrypt SSL

---

## 🔧 1. Install SFTPGo

### Ubuntu / Debian
```bash
apt update
apt install sftpgo -y
````

### RHEL / AlmaLinux

```bash
dnf install sftpgo -y
```

### ▶️ Start Service

```bash
systemctl enable sftpgo
systemctl start sftpgo
systemctl status sftpgo
```

### 👉 Default Ports

* **Web UI**: 8080
* **SFTP**: 2022

---

## 🌐 2. Configure Domain + Nginx + Let’s Encrypt

---

### ✅ Step 2.1: Install Nginx + Certbot

```bash
# RHEL / AlmaLinux
dnf install nginx certbot python3-certbot-nginx -y

# Ubuntu / Debian
apt install nginx certbot python3-certbot-nginx -y
```

---

### ✅ Step 2.2: Configure Nginx Reverse Proxy

Edit config:

```bash
vi /etc/nginx/conf.d/sftpgo.conf
```

Add:

```nginx
server {
    server_name test.gokultech.shop;

    location / {
        proxy_pass http://127.0.0.1:8080;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
    }
}
```

Restart Nginx:

```bash
systemctl restart nginx
```

---

### ✅ Step 2.3: DNS Check (IMPORTANT)

Make sure your domain points to your server's **public IP**:

```bash
nslookup test.gokultech.shop
```

---

### ✅ Step 2.4: Open Firewall

```bash
firewall-cmd --permanent --add-service=http
firewall-cmd --permanent --add-service=https
firewall-cmd --reload
```

---

### ✅ Step 2.5: Get SSL Certificate

```bash
certbot --nginx -d test.gokultech.shop
```

---

### 📄 Final Nginx Configuration

```nginx
server {
    listen 80;
    server_name test.gokultech.shop;

    # Redirect HTTP to HTTPS
    return 301 https://$host$request_uri;
}

server {
    listen 443 ssl;
    server_name test.gokultech.shop;

    ssl_certificate /etc/letsencrypt/live/test.gokultech.shop/fullchain.pem;
    ssl_certificate_key /etc/letsencrypt/live/test.gokultech.shop/privkey.pem;
    include /etc/letsencrypt/options-ssl-nginx.conf;
    ssl_dhparam /etc/letsencrypt/ssl-dhparams.pem;

    location / {
        proxy_pass http://127.0.0.1:8080;

        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;

        # Recommended headers
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
    }
}
```

---

## 🔥 Final Access

👉 Open in browser:

```
https://test.gokultech.shop
```

### ✔️ This setup will:

* Install SSL certificate
* Auto-configure Nginx
* Enable HTTPS access

---

## 🧪 Verification Commands

### Check Nginx config

```bash
nginx -t
systemctl reload nginx
```

### Check Ports

```bash
ss -tulnp | grep 8080
ss -tulnp | grep 80
ss -tulnp | grep 443
```

### Test Services

```bash
curl -I http://localhost:8080
curl -I http://localhost:80
curl -I https://test.gokultech.shop
```

---

```


