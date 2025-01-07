# Installing and Configuring Nginx with Reverse Proxy and Cloudflare SSL on Ubuntu 24 (or Ubuntu 22)

Follow these steps to install and configure **Nginx** on Ubuntu 24 (Also tested on ubuntu 22), set up a reverse proxy for `domain.com` (serving traffic for a Docker container on port 3000), and use Cloudflare certificates for HTTPS on port 443.

---

## **1. Install Nginx**

1. Update system packages:
   ```bash
   sudo apt update && sudo apt upgrade -y
   ```
2. Install Nginx:
   ```bash
   sudo apt install nginx -y
   ```
3. Enable and start Nginx:
   ```bash
   sudo systemctl enable nginx
   sudo systemctl start nginx
   ```

## **2. Obtain Cloudflare Certificate and Key**

1. Log in to your Cloudflare account and navigate to the SSL/TLS > Origin Server section.
2. Create a new Origin Certificate:
   - Choose "Let Cloudflare generate a private key and a CSR."
   - Set validity (e.g., 15 years).
   - Add your domain (`domain.com`) and optionally subdomains.
3. Download the certificate (`origin.pem`) and private key (`origin-key.pem`).

## **3. Upload the Certificate and Key to Your Server**

1. Transfer the files to the server using `scp` or manually upload them. They are small files, you can copy paste their content too.:
   ```bash
   scp origin.pem origin-key.pem user@your-server:/etc/nginx/ssl/
   ```
2. Move them to the appropriate directory:
   ```bash
   sudo mkdir -p /etc/nginx/ssl
   sudo mv origin.pem /etc/nginx/ssl/domain.com.pem
   sudo mv origin-key.pem /etc/nginx/ssl/domain.com.key
   ```
3. Set proper permissions:
   ```bash
   sudo chmod 600 /etc/nginx/ssl/*
   ```

## **4. Configure Nginx as a Reverse Proxy**

1. Create an Nginx configuration file for your domain:
   ```bash
   sudo nano /etc/nginx/sites-available/domain.com
   ```
2. Add the following configuration. If you have set https mode to Full, then there is no need to listen to port 80:

   ```nginx
   server {
       listen 80;
       server_name domain.com;

       # Redirect HTTP to HTTPS
       return 301 https://$host$request_uri;
   }

   server {
       listen 443 ssl;
       server_name domain.com;

       ssl_certificate /etc/nginx/ssl/domain.com.pem;
       ssl_certificate_key /etc/nginx/ssl/domain.com.key;
       ssl_protocols TLSv1.2 TLSv1.3;
       ssl_ciphers HIGH:!aNULL:!MD5;

       location / {
           proxy_pass http://localhost:3000;
           proxy_http_version 1.1;
           proxy_set_header Upgrade $http_upgrade;
           proxy_set_header Connection 'upgrade';
           proxy_set_header Host $host;
           proxy_cache_bypass $http_upgrade;
       }
   }
   ```

3. Enable the configuration:
   ```bash
   sudo ln -s /etc/nginx/sites-available/domain.com /etc/nginx/sites-enabled/
   ```

## **5. Test and Reload Nginx**

1. Test the configuration for syntax errors:
   ```bash
   sudo nginx -t
   ```
2. Reload Nginx:
   ```bash
   sudo systemctl reload nginx
   ```

## **6. Configure Firewall**

1. Allow HTTP and HTTPS traffic:
   ```bash
   sudo ufw allow 'Nginx Full'
   ```
2. Check the firewall status:

   ```bash
   sudo ufw status
   ```

   You can also use [my script](https://gist.github.com/miiiladiii244/21f2f89d55f08f67664fda5f7b476500) to automattically only allow Cloudflare ips.

## **7. Test the Setup**

1. Ensure your Docker container (or any web server) is running on port 3000:
   ```bash
   docker run -d -p 3000:3000 your-docker-image
   ```
2. Verify the domain:
   - Access `https://domain.com` in your browser.

## **8. Optional: Enable Automatic Updates for Nginx**

1. Install `unattended-upgrades` (if not installed):
   ```bash
   sudo apt install unattended-upgrades
   ```
2. Enable automatic updates for security patches:
   ```bash
   sudo dpkg-reconfigure --priority=low unattended-upgrades
   ```

---

This setup will redirect HTTP traffic to HTTPS, use Cloudflare's SSL for secure connections, and proxy requests to the Docker container on port 3000.
