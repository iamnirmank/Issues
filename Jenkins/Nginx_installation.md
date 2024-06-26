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
## Securing Nginx
```
https://www.digitalocean.com/community/tutorials/how-to-secure-nginx-with-let-s-encrypt-on-ubuntu-16-04
```
