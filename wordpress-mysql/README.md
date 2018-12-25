Just a simple Wordpress deploy with custom config json file option, Nginx as reverse proxy is needed.

Initiate local 'storage' with:

```
mkdir -p db_data/
mkdir -p wordpress/
```

Nginx on Host configuration example:

```
server {
    listen 80;
    server_name wordpress.nova.moe;

	location / {
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header HOST $http_host;
        proxy_set_header X-NginX-Proxy true;

        proxy_pass http://127.0.0.1:2400;
        proxy_redirect off;
	}
}
```
