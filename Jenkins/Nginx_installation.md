## Install Nginx
```
sudo apt-get update
sudo apt-get install nginx
```

## Adjust Firewall
```
sudo ufw app list
sudo ufw allow 'Nginx HTTP'
sudo ufw enable
sudo ufw status
```

## Check Oour Webserver
```
sudo systemctl stop nginx
sudo systemctl start nginx
sudo systemctl restart nginx
sudo systemctl reload nginx
systemctl status nginx
sudo systemctl disable nginx
sudo systemctl enable nginx
```
# Securing Nginx
## Installing Certbot
```
<!-- sudo add-apt-repository ppa:certbot/certbot --> This one is not needed normally.
sudo apt-get update
sudo apt-get install python-certbot-nginx
```
Got issue? (E: Unable to locate package python-certbot-nginx) then follow this:
```
sudo add-apt-repository --remove ppa:certbot/certbot
sudo apt-get update
sudo apt-get install certbot python3-certbot-nginx
```
## Setting Up Nginx
```
sudo nano /etc/nginx/sites-available/web.midasstock.com.np

server {
    listen 80;
    server_name web.midasstock.com.np;

    location / {
        proxy_pass http://localhost:3001;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
    }
}
```
## Enable the Configuration Files
```
sudo ln -s /etc/nginx/sites-available/web.midasstock.com.np /etc/nginx/sites-enabled/
sudo nginx -t
sudo systemctl reload nginx
```
## Allow HTTPS Through the Firewall
```
sudo ufw allow 'Nginx Full'
sudo ufw delete allow 'Nginx HTTP'
sudo ufw status

sudo certbot --nginx -d web.midasstock.com.np -v
sudo certbot --nginx -d backend.midasstock.com.np -v

sudo certbot renew --dry-run
```


