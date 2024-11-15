## Set up Docker's apt repository.
```
# Add Docker's official GPG key:
sudo apt-get update
sudo apt-get install ca-certificates curl
sudo install -m 0755 -d /etc/apt/keyrings
sudo curl -fsSL https://download.docker.com/linux/ubuntu/gpg -o /etc/apt/keyrings/docker.asc
sudo chmod a+r /etc/apt/keyrings/docker.asc

# Add the repository to Apt sources:
echo \
  "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.asc] https://download.docker.com/linux/ubuntu \
  $(. /etc/os-release && echo "$VERSION_CODENAME") stable" | \
  sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
sudo apt-get update
```

## Install the Docker Package
```
sudo apt-get install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin
```
## Start and Verify
```
sudo service docker start
sudo docker run hello-world
```
 # Install Docke Compose
 ## Install Docker Compose
 ```
sudo curl -L "https://github.com/docker/compose/releases/download/v2.3.3/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose
sudo chmod +x /usr/local/bin/docker-compose
```
## Verify installation
```
docker-compose --version
```

# Add Docker Compose to Jenkins User's PATH
## Global Path Update
```
echo 'export PATH=$PATH:/usr/local/bin' | sudo tee -a /etc/profile
source /etc/profile

sudo systemctl restart jenkins

```
## Making Not to Use Sudo
```
sudo usermod -aG docker jenkins
sudo systemctl restart jenkins
```