# Jenkins Installation
## Add GPG Key using Command
```
wget -q -O - http://pkg.jenkins-ci.org/debian/jenkins-ci.org.key | sudo apt-key add -
```
### Add Repository
```
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
sudo service jenkins stop
```
### Get Detailed Information about every Logs for debugging purpose
```
journalctl -xeu jenkins.service --no-pager
```
## Find and Copy the first time password of Jenkins Server
```
sudo cat /var/lib/jenkins/secrets/initialAdminPassword
```
Finally: Access the Jenkins URL: HTTP://YOUR_IP:8080

# Example Issue: JAVA version Issue: 
Jun 26 04:02:11 midasvps jenkins[46421]: Running with Java 11 from /usr/lib/jvm/java-11-openjdk-amd64, which is older than the minimum required version (Java 17).
Jun 26 04:02:11 midasvps jenkins[46421]: Supported Java versions are: [17, 21]
```
sudo apt update
sudo apt install openjdk-17-jdk
sudo update-alternatives --config java
sudo systemctl restart jenkins
sudo systemctl status jenkins.service
```

## More Reference:
```
https://www.digitalocean.com/community/tutorials/how-to-set-up-continuous-integration-pipelines-in-jenkins-on-ubuntu-16-04
```