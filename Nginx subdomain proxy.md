# Put a port on a subdomain using Nginx

> **Using:** Nginx
> 
> **System:** Fedora

## Config file

location: `/etc/nginx/conf.d/abcd.conf`

```bash
server {
       server_name abcd.orphoros.com;
       #root /usr/share/nginx/html;
       access_log /var/log/nginx/abcd.access;
       error_log /var/log/nginx/abcd.error;

       location / {
              proxy_pass      https://127.0.0.1:10000;

              #Proxy Settings
              proxy_redirect     off;
              proxy_set_header   Host             $host:$server_port;
              proxy_set_header   X-Real-IP        $remote_addr;
              proxy_set_header   X-Forwarded-For  $proxy_add_x_forwarded_for;
        }
}
```

## Use SSL
```bash
certbot --nginx -d abcd.orphoros.com
```

## Check config status
```bash
nginx -t
```