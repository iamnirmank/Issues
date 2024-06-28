## Change Posgresql User Password
```
sudo -i
passwd 
exit
```
## Configure PostgreSQL
```
sudo -i -u postgres
psql
CREATE USER youruser WITH PASSWORD 'yourpassword';
CREATE DATABASE yourdatabase;
GRANT ALL PRIVILEGES ON DATABASE yourdatabase TO youruser;
\q
exit
```
## Configure PostgreSQL to Allow Remote Connections
```
sudo nano /etc/postgresql/12/main/postgresql.conf
#listen_addresses = 'localhost' To listen_addresses = '*'

sudo nano /etc/postgresql/12/main/pg_hba.conf
host    all             all             0.0.0.0/0               md5

sudo systemctl restart postgresql
sudo ufw allow 5432/tcp
```

## Example
```
psql -h 103.243.100.79 -U midasdbcomnp -d midasstockdatabase
```
