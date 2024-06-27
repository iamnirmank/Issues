## Generate Access Token
```
Profile > Settings > Developer settings > Personal access tokens > Tokens
```
## Add SSH Key
```
Profile > Settings > SSH and GPG keys > New SSH key
```
## WebHooks
```
GitHub Repo > Settings > Webhooks > Add Webhooks
```
## Generate a New SSH Key to Add to your GitHub
``` 
ssh-keygen -t rsa -b 4096 -C "your_email@example.com"
eval "$(ssh-agent -s)"
ssh-add ~/.ssh/id_rsa
cat ~/.ssh/id_rsa.pub
``` 
Test:
```
ssh -T git@github.com
```