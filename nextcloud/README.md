Just a simple Nextcloud deploy , Nginx as reverse proxy is needed.

Initiate local 'storage' with:

```
mkdir db_data nextcloud data
```

Nginx on Host configuration example:

```
server {
    listen 80;
    server_name nextcloud.nova.moe;

        location / {
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header HOST $http_host;
        proxy_set_header X-NginX-Proxy true;

        proxy_pass http://127.0.0.1:2403;
        proxy_redirect off;
        }
}
```

