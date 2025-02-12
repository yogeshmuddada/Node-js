----------

## **1. Upload Your Node.js Code to the Server**

Use tools like `scp` or `git` to transfer your Node.js project to the server.

```bash
scp -r /path/to/your/project user@server_ip:/path/to/destination

```

Alternatively, if using Git:

```bash
git clone <your-repo-url>

```

----------

## **2. Install Node.js and npm on the Server**

Check if Node.js is installed:

```bash
node -v

```

If not installed, you can do it using NodeSource repository:

```bash
curl -fsSL https://deb.nodesource.com/setup_18.x | sudo -E bash -
sudo apt install nodejs

```

----------

## **3. Install Project Dependencies**

Navigate to your project directory and install dependencies:

```bash
cd /path/to/your/project
npm install

```

----------

## **4. Start Your Node.js Application**

Start your application using a process manager like PM2 to ensure it runs in the background.

### Install PM2:

```bash
sudo npm install pm2 -g

```

### Start the App:

```bash
pm2 start app.js --name your_app_name
pm2 save
pm2 startup

```

This keeps the app running even after a server reboot.

----------

## **5. Configure Nginx as a Reverse Proxy**

Edit the Nginx configuration file:

```bash
sudo nano /etc/nginx/sites-available/default

```

Add the following configuration:

```nginx
server {
    listen 80;
    server_name your_domain_or_ip;

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

Change `3000` to your Node.js app's port if different.

----------

## **6. Test and Restart Nginx**

### Test the Configuration:

```bash
sudo nginx -t

```

### Restart Nginx:

```bash
sudo systemctl restart nginx

```

----------

## **7. Adjust Firewall Rules (if needed)**

Ensure HTTP traffic is allowed:

```bash
sudo ufw allow 'Nginx Full'

```

----------

## **8. Access the Application**

Now, open your browser and navigate to `http://your_domain_or_ip`. Your Node.js app should be accessible.

----------

### **Optional: Enable HTTPS (Recommended for Production)**

Use Certbot to secure your site with SSL:

```bash
sudo apt install certbot python3-certbot-nginx
sudo certbot --nginx -d your_domain

```

----------

If you face any issues during deployment or configuration, let me know, and I'll help you troubleshoot them!



-----

## **Alternative to PM2**

If you don't want to use PM2, you can use **systemd** to manage the Node.js process. Here's how:

### 1. **Create a Systemd Service**

Create a new service file:

```bash
sudo nano /etc/systemd/system/node-app.service

```

### 2. **Add the Following Configuration:**

```ini
[Unit]
Description=Node.js App

[Service]
ExecStart=/usr/bin/node /path/to/your/app.js
Restart=always
User=nobody
Environment=PORT=3000
WorkingDirectory=/path/to/your/project

[Install]
WantedBy=multi-user.target

```

### 3. **Enable and Start the Service:**

```bash
sudo systemctl daemon-reload
sudo systemctl start node-app
sudo systemctl enable node-app

```

### 4. **Check Status:**

```bash
sudo systemctl status node-app

```
