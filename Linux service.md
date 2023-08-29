# Make a service on linux

> **Using:** systemctl
> 
> **System:** Fedora

## Create service

```bash
nano /etc/systemd/system/my-service.service
```
## Write the service
```toml
[Unit]
Description=My Service
After=rc-local.service

[Service]
Type=simple
Restart=on-failure
ExecStart=/script.sh
User=runner-user

[Install]
WantedBy=multi-user.target
```
## Start the service
```bash
systemctl daemon-reload
systemctl start my-service
systemctl enable my-service
systemctl status my-service
```