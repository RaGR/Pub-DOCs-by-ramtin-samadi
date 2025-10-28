#install and configure NGINX on Ubuntu using terminal commands.

### Step 1: Update the System

Before installing NGINX, it’s a good idea to update your package list:

```bash
sudo apt update
```

### Step 2: Install NGINX

Now, install NGINX with the following command:

```bash
sudo apt install nginx
```

### Step 3: Start NGINX

After installation, you can start NGINX using:

```bash
sudo systemctl start nginx
```

### Step 4: Enable NGINX to Start on Boot

To ensure NGINX starts automatically on boot, use:

```bash
sudo systemctl enable nginx
```

### Step 5: Check NGINX Status

You can verify that NGINX is running with:

```bash
sudo systemctl status nginx
```

Press `q` to exit the status output.

### Step 6: Configure Firewall

If you have a firewall enabled, you’ll need to allow HTTP and HTTPS traffic. Depending on your setup, you can use `ufw`:

```bash
sudo ufw allow 'Nginx Full'
```

### Step 7: Verify Installation

Open your web browser and go to `http://your_server_ip/`. You should see the default NGINX welcome page if everything is set up correctly! 🎉

### Step 8: Basic NGINX Configuration

The main configuration file is located at `/etc/nginx/nginx.conf`, and the default server block configuration is in `/etc/nginx/sites-available/default`. 

To edit the default configuration, use your favorite text editor (e.g., `nano`):

```bash
sudo nano /etc/nginx/sites-available/default
```

Here’s a basic example of what you might change or add:

```nginx
server {
    listen 80;  # Port to listen on
    server_name your_domain_or_IP;  # Change to your domain or server IP
    
    root /var/www/html;  # Directory for your website files
    index index.html index.htm;  # Default index files

    location / {
        try_files $uri $uri/ =404;  # Handle requests
    }

    error_page 404 /404.html;  # Custom error page
}
```

### Step 9: Test NGINX Configuration

Before applying changes, test the configuration for syntax errors:

```bash
sudo nginx -t
```

### Step 10: Reload NGINX

If the configuration test is successful, reload NGINX to apply the changes:

```bash
sudo systemctl reload nginx
```

### Additional Configuration (Optional)

- **Setting Up SSL**: If you want to set up SSL/TLS with Let’s Encrypt, you can use Certbot for automatic configuration. You can install it with:

  ```bash
  sudo apt install certbot python3-certbot-nginx
  ```

  Then run:

  ```bash
  sudo certbot --nginx
  ```

- **Virtual Hosts**: You can create new configuration files in the `sites-available` directory and then link them to `sites-enabled` using:

  ```bash
  sudo ln -s /etc/nginx/sites-available/your_config /etc/nginx/sites-enabled/
  ```

### Conclusion

You have now installed and configured NGINX on your Ubuntu system! 🎉 If you have any specific needs or additional questions, feel free to ask! 😊
