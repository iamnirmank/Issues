## Add Credentials
Create the secret key
```
Profile>Developer Settings>Personal access tokens>Tokens(classic)
```
Add to the Jenkins Credentials
```
Manage Jenkins>credentials>DOmains(global)>Username with password (Password == Secret Key)
```

## Add continuous integration item
```
+ New Item > Freestyle project
```

## Configure the project
```
Source Code Management > Git > 
- Repository URL and Credentials
- Branch Name Build Triggers (GitHub hook trigger for GITScm polling) 
- Specify the Command without using Sudo
```
## Making Not to use sudo
```
sudo usermod -aG docker jenkins
sudo systemctl restart jenkins
```
## GitHub Webhook for the Continuous Updates between Jenkins and Github
```
GitHub Repo > Settings > Webhooks > Add webhook > 
- Payload URL = https://<jenkins-url>/github-webhook/
- Content type = application/json
- Let me select individual events = Pull requests, and pushes
```