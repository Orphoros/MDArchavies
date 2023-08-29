# Install MongoDB on Fedora

## Install `MongoDB`:

```bash
sudo nano /etc/yum.repos.d/mongodb-org-4.4.repo
```

Add the following content:

```bash
[Mongodb]
name=MongoDB Repository
baseurl=https://repo.mongodb.org/yum/redhat/8/mongodb-org/4.4/x86_64/
gpgcheck=1
enabled=1
gpgkey=https://www.mongodb.org/static/pgp/server-4.4.asc
```

Install `MongoDB`:

```bash
sudo dnf install mongodb-org
```

Start the service

```bash
sudo systemctl daemon-reload
sudo systemctl start mongod
sudo systemctl enable mongod
```

Check if MongoDB is running:
    
```bash
mongod -version
```

## Configure MongoDB

Add DB user by entering the `mongo` command (enters the mongo shell):

```bash
use admin
db.createUser(
  {
    user: "dbAdmin",
    pwd: "MyPassword987",
    roles: [ { role: "userAdminAnyDatabase", db: "admin" }, "readWriteAnyDatabase" ]
  }
)
exit
```

Combine certs for mongo to use:

```bash
cat>/root/renew-mongodb-cert.sh <<\EOF
#!/bin/bash
HOSTNAME="abc.orphoros.com"
cat /etc/letsencrypt/live/$HOSTNAME/fullchain.pem /etc/letsencrypt/live/$HOSTNAME/privkey.pem > /etc/ssl/mongodb.pem
chmod 644 /etc/ssl/mongodb.pem
service mongod restart
EOF
chmod +x /root/renew-mongodb-cert.sh
```

Setup job to remake certs
```bash
crontab -e
```
```bash
SHELL=/bin/sh
PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/root/bin

0 0,12 * * * certbot renew --renew-hook /root/renew-mongodb-cert.sh > /root/certbot-cron.log 2>&1
```

Edit `/etc/mongod.conf`

```bash
# network interfaces
net:
  port: 27017
  bindIp: 0.0.0.0 # enter 0.0.0.0,:: to bind to all IPv4 and IPv6 addresses or, alternatively, use the net.bindIpAll setting.
  ssl:
    mode: requireSSL
    PEMKeyFile: /etc/ssl/mongodb.pem

security:
  authorization: enabled
```