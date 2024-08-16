# ARR Stack Docker Compose Configurations

This repository contains two different approaches to setting up an ARR stack (Sonarr, Radarr, Jackett, and Overseerr) using Docker Compose:

1. Automatic Proxy with Traefik
2. Manual Proxy with Nginx

## 1. Automatic Proxy with Traefik

### File: docker-compose.traefik.yml

This configuration uses Traefik as a reverse proxy and automatically handles SSL certificates using Let's Encrypt.

### How to use:

1. Rename the file:
   ```
   mv docker-compose.auto.proxy.traefik.yml docker-compose.traefik.yml
   ```

2. Edit the file:
   - Replace `yourdomain.com` with your actual domain name for each service.
   - Replace `youremail@example.com` in the Traefik service with your email address (for Let's Encrypt notifications).

3. Ensure that your domain (and subdomains) are pointing to the IP address of the server running this Docker Compose setup.

4. Make sure ports 80 and 443 are open and forwarded to your server if it's behind a router or firewall.

5. Create the external Docker network:
   ```
   docker network create arrstack
   ```

6. Start the services:
   ```
   docker-compose -f docker-compose.traefik.yml up -d
   ```

## 2. Manual Proxy with Nginx

### File: docker-compose.nginx.yml

This configuration requires manual setup of Nginx as a reverse proxy on the host machine. The main benefit of this is if
you already have an Nginx server running and want to integrate these services into your existing setup, or, if you want
to run the services on a different machine than the one running the reverse proxy.

### How to use:

1. Rename the file:
   ```
   mv docker-compose.manual.proxy.nginx.yml docker-compose.nginx.yml
   ```

2. Install Nginx on your host machine if not already installed.

3. Generate self-signed SSL certificate and key:
   ```
   sudo openssl req -x509 -nodes -days 365 -newkey rsa:2048 -keyout /etc/nginx/ssl/nginx.key -out /etc/nginx/ssl/nginx.crt
   ```

4. Create a new Nginx configuration file:
   ```
   sudo nano /etc/nginx/sites-available/arr-stack.conf
   ```

5. Copy the server blocks from the comments in the docker-compose file into this new Nginx configuration file, replacing 'yourdomain.com' with your actual domain.

6. Create symbolic links to enable the site:
   ```
   sudo ln -s /etc/nginx/sites-available/arr-stack.conf /etc/nginx/sites-enabled/
   ```

7. Test the Nginx configuration:
   ```
   sudo nginx -t
   ```

8. If the test is successful, reload Nginx:
   ```
   sudo systemctl reload nginx
   ```

9. Set up DNS A records for each subdomain, pointing to your server's IP address.

10. Create the external Docker network:
    ```
    docker network create arrstack
    ```

11. Start the services:
    ```
    docker-compose -f docker-compose.nginx.yml up -d
    ```

For production use, replace self-signed certificates with trusted ones (e.g., Let's Encrypt).

## Accessing Your Services

After setting up either configuration, you can access your services at:

- https://jackett.yourdomain.com
- https://overseerr.yourdomain.com
- https://radarr.yourdomain.com
- https://sonarr.yourdomain.com

Remember to replace 'yourdomain.com' with your actual domain name.
