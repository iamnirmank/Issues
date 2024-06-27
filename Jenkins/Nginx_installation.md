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

## Check your webserver
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
sudo add-apt-repository ppa:certbot/certbot
sudo apt-get update
sudo apt-get install python-certbot-nginx
```
Got issue? (E: Unable to locate package python-certbot-nginx) then follow this:
```
sudo add-apt-repository --remove ppa:certbot/certbot
sudo apt-get update
sudo apt-get install certbot python3-certbot-nginx
```
## Setting up Nginx
```
sudo nano /etc/nginx/sites-available/default
.....
server_name jenkins.midasstock.com.np backend.midasstock.com.np training.midasstock.com.np panel.midasstock.com.np web.midasstock.com.np;
.....

sudo nginx -t
sudo systemctl reload nginx
```
## Allow HTTPS Through the Firewall
```
sudo ufw allow 'Nginx Full'
sudo ufw delete allow 'Nginx HTTP'
sudo ufw status

sudo certbot --nginx -d jenkins.midasstock.com.np -d backend.midasstock.com.np -d training.midasstock.com.np -d panel.midasstock.com.np -d web.midasstock.com.np -v
sudo certbot renew --dry-run
```


