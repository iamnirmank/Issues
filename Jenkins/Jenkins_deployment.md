## Add Credentials
Create the secret key
```
Profile > Developer Settings > Personal access tokens > Tokens(classic)
```
Add to the Jenkins Credentials
```
Manage Jenkins > credentials>DOmains(global) > Username with password (Password == Secret Key)
```

## Add Project Item
```
+ New Item > Freestyle project
```
## Configure the Project
```
Source Code Management > Git > 
- Repository URL and Credentials
- Branch Name Build Triggers (GitHub hook trigger for GITScm polling) 
- Specify the Command without using Sudo
```
## GitHub Webhook for the Continuous Updates between Jenkins and Github
```
GitHub Repo > Settings > Webhooks > Add webhook > 
- Payload URL = https://<jenkins-url>/github-webhook/
- Content type = application/json
- Let me select individual events = Pull requests, and pushes
```

## Build Steps
Build Steps > Execute shell
```
docker-compose down
docker-compose up -d --force-recreate --no-deps --build backend
```
