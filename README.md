# Nginx Node.js Reverse Proxy Project

This project sets up a **Nginx reverse proxy** to serve a Node.js backend and a static frontend. It allows handling of API requests and WebSocket connections through Nginx.

## Project Structure

```
/nginx-node-proxy
  /frontend       # Static frontend files
  /backend        # Node.js backend server
```

## Frontend Setup

1. Create a frontend directory:

```bash
sudo mkdir -p /var/www/frontend
```

2. Copy your frontend files:

```bash
sudo cp -r frontend/* /var/www/frontend/
```

3. Set permissions:

```bash
sudo chown -R www-data:www-data /var/www/frontend
```

## Nginx Configuration

1. Open Nginx sites-available:

```bash
sudo vi /etc/nginx/sites-available/nginx-node-proxy
```

2. Add the following configuration:

```nginx
server {
    listen 80;
    server_name <domain name>; # Use _ or your IP if no domain yet

    root /var/www/frontend;
    index index.html;

    # Serve static files
    location / {
        try_files $uri $uri/ =404;
    }

    # Reverse proxy to Node.js API
    location /api/ {
        proxy_pass http://13.201.37.133:3000;
        proxy_http_version 1.1;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
    }

    # WebSocket support
    location /socket.io/ {
        proxy_pass http://localhost:3000;
        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection "upgrade";
        proxy_set_header Host $host;
    }
}
```

## Backend Setup

1. Go to backend folder:

```bash
cd /home/ubuntu/nginx-node-proxy/backend
```

2. Initialize Node.js project:

```bash
npm init -y
```

3. Install dependencies:

```bash
npm install express cors socket.io
```

4. Start the backend:

```bash
node index.js
```

## Enable Nginx Site and Reload

```bash
sudo ln -s /etc/nginx/sites-available/nginx-node-proxy /etc/nginx/sites-enabled/
sudo nginx -t
sudo systemctl reload nginx
```

## Access

Open your browser and navigate to your domain: `http://<domain name>`

---

**Notes:**

* Make sure Node.js backend is running.
* Adjust IPs and ports according to your server setup.
* Static frontend is served by Nginx, while API and WebSocket requests are proxied to Node.js backend.

---

## Author

Hanzala Israr 
