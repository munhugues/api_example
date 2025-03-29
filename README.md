# Dictionary App

## Overview
This project is a web-based dictionary application that fetches word definitions using an external API. The application allows users to search for word meanings interactively and is deployed on a load-balanced server infrastructure.

## Features
- Users can search for word definitions
- Interactive UI with real-time API requests
- Error handling for API failures
- Deployed on two web servers with a load balancer

## Technologies Used
- **Frontend:** HTML, CSS, JavaScript
- **API:** Dictionary API (e.g., Free Dictionary API)
- **Servers:** Nginx (as web server and load balancer)
- **Deployment:** Linux-based web servers (web01(54.167.166.210), web02(44.204.83.187)), Load Balancer (3.80.150.90)

## Local Setup
To run the application locally:

1. Clone the repository:
   ```sh
   git clone https://github.com/munhugues/api_example.git
   cd dictionary-app
   ```
2. Open `index.html` in a browser.
3. Ensure you have an active internet connection for API requests.

## Deployment Steps

### 1. Server Configuration
- SSH into both web servers:
  ```sh
  ssh ubuntu@54.167.166.210
  ssh ubuntu@44.204.83.18
  ```
- Create a directory for the app:
  ```sh
  mkdir -p /var/www/dictionary
  ```
- Upload project files to both servers:
  ```sh
  scp -r /local/path/dictionary ubuntu@54.167.166.210:/var/www/
  scp -r /local/path/dictionary ubuntu@44.204.83.18:/var/www/
  ```

### 2. Web Server (Nginx) Setup
- Configure Nginx on each web server:
  ```sh
  sudo nano /etc/nginx/sites-available/dictionary
  ```
- Add the following:
  ```nginx
  server {
      listen 80;
      server_name munhugues.tech www.munhugues.tech;
      root /var/www/dictionary;
      index index.html index.htm;
  }
  ```
- Enable the configuration and restart Nginx:
  ```sh
  sudo ln -s /etc/nginx/sites-available/dictionary /etc/nginx/sites-enabled/
  sudo systemctl restart nginx
  ```

### 3. Load Balancer Setup
- SSH into the load balancer:
  ```sh
  ssh ubuntu@3.80.150.90
  ```
- Open the Nginx config:
  ```sh
  sudo nano /etc/nginx/nginx.conf
  ```
- Add this configuration:
  ```nginx
  upstream dictionary_app {
      server 54.167.166.210;
      server 44.204.83.18;
  }
  
  server {
      listen 80;
      server_name munhugues.tech www.munhugues.tech;
      location / {
          proxy_pass http://dictionary_app;
      }
  }
  ```
- Restart Nginx:
  ```sh
  sudo systemctl restart nginx
  ```

### 4. Domain and SSL Configuration
- Set up an **A record** in your DNS provider pointing to the Load Balancer IP.
- Install SSL using Let’s Encrypt:
  ```sh
  sudo apt install certbot python3-certbot-nginx
  sudo certbot --nginx -d yourdomain.com -d www.yourdomain.com
  ```
- Test SSL auto-renewal:
  ```sh
  sudo certbot renew --dry-run
  ```

## API Used
- **Name:** Free Dictionary API
- **Documentation:** (https://dictionaryapi.dev/)

## Demo Video
[(https://youtu.be/HAUhXlFPiOw)]

## Credits
- Free Dictionary API Developers
- Nginx Documentation
- Linux Server Setup Tutorials


