# Jenkins Installation
## Add GPG Key and Repository Using Command
```
wget -q -O - http://pkg.jenkins-ci.org/debian/jenkins-ci.org.key | sudo apt-key add -
sudo sh -c 'echo deb http://pkg.jenkins-ci.org/debian binary/ > /etc/apt/sources.list.d/jenkins.list'
```
### Update Source List
```
sudo apt-get update
```
### Install Jenkins
```
sudo apt-get install jenkins
```
Error While Installing: GPG error: https://pkg.jenkins.io/debian binary/ Release: The following signatures couldn't be verified because the public key is not available:
```
sudo apt-key adv --keyserver keyserver.ubuntu.com --recv-keys 5BA31D57EF5975CA
sudo apt-get update
```
### Download and add GPG Key
```
wget -q -O jenkins.io.key https://pkg.jenkins.io/debian-stable/jenkins.io.key
sudo apt-key add jenkins.io.key
```
### Start, Enable, Check Status, Or Stop the Jenkins Server
```
sudo systemctl start jenkins
sudo systemctl enable jenkins
sudo systemctl status jenkins.service
sudo systemctl restart jenkins.service
sudo service jenkins stop
```
### Get Detailed Information about Every Logs for Debugging Purpose
```
journalctl -xeu jenkins.service --no-pager
```
## Find and Copy the First Time Password of Jenkins Server
```
sudo cat /var/lib/jenkins/secrets/initialAdminPassword
```
Finally: Access the Jenkins URL: HTTP://YOUR_IP:8080

# Issue During Starting? Do this:
That error is because of the Java version because Supported Java versions are: [17, 21]
```
sudo apt update
sudo apt install openjdk-17-jdk
sudo update-alternatives --config java
sudo systemctl restart jenkins
sudo systemctl status jenkins.service
```

# Configure Jenkins with SSL using an Nginx Reverse Proxy
## Configuring Nginx
```
sudo nano /etc/nginx/sites-available/default

. . . 
server {
        # SSL Configuration
        #
        # listen 443 ssl default_server;
        # listen [::]:443 ssl default_server;
        access_log            /var/log/nginx/jenkins.access.log;
        error_log            /var/log/nginx/jenkins.error.log;
}
```
comment out the default try_files line which, as written, will return a 404 error before the request reaches Jenkins:
```
. . .
           location / {
                # First attempt to serve request as file, then
                # as directory, then fall back to displaying a 404.
                # try_files $uri $uri/ =404;        }
. . . 
```
Be sure to substitute your SSL-secured domain name in the proxy_redirect:
```
Location /  
. . .
           location / {
                # First attempt to serve request as file, then
                # as directory, then fall back to displaying a 404.
                # try_files $uri $uri/ =404;
                include /etc/nginx/proxy_params;
                proxy_pass          http://localhost:8080;
                proxy_read_timeout  90s;
                # Fix potential "It appears that your reverse proxy set up is broken" error.
                proxy_redirect      http://localhost:8080 https://your.ssl.domain.name;
           }

sudo nginx -t
```
## Configuring Jenkins
```
sudo nano /etc/default/jenkins
. . .
JENKINS_ARGS="--webroot=/var/cache/$NAME/war --httpPort=$HTTP_PORT --httpListenAddress=127.0.0.1"

sudo systemctl restart jenkins
sudo systemctl status jenkins
sudo systemctl restart nginx
sudo systemctl status nginx
```
