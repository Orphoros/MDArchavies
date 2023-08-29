# Automatically ban IP addresses

> **Using:** Fail2Ban
> 
> **System:** Fedora

## Setup

Install `Fail2Ban`:

```bash
sudo systemctl start fail2ban
sudo systemctl enable fail2ban
sudo systemctl status fail2ban
```

Install `firewalld`:

```bash
sudo systemctl start firewalld
sudo systemctl enable firewalld
sudo systemctl status firewalld
```

## Edit config

Location: `/etc/fail2ban/jail.local`

Example config for SSH and Webmin:

```toml
[DEFAULT]
bantime = 21600
bantime.increment = true
bantime.factor = 1
bantime.formula = ban.Time * (1<<(ban.Count if ban.Count<20 else 20)) * banFactor
maxretry = 3

[sshd]
enabled = true
port = 22
filter = sshd
logpath = /var/log/fail2ban.log

[webmin-auth]
enabled = true
port    = 10000
filter  = webmin-auth
logpath = /var/log/fail2ban.log
```

## Check banned IP times
```bash
fail2ban-client get sshd banip --with-time
```

## Check banned IP list
```bash
fail2ban-client status sshd
```

## Log location
```bash
/var/log/fail2ban.log
```