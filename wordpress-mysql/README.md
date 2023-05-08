Just a simple Wordpress deploy with custom config json file option, Nginx as reverse proxy is needed.

To change the upload file size, Add the following lines in `wordpress/.htaccess`, inside `<IfModule mod_rewrite.c>` and `</IfModule>`.

```
php_value post_max_size 2004M
php_value upload_max_filesize 200M
```

Nginx on Host configuration example:

```
server {
    listen 80;
    server_name wordpress.nova.moe;

	location / {
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header HOST $http_host;
		proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
		proxy_set_header X-Forwarded-Proto $scheme;
        proxy_set_header X-NginX-Proxy true;

        proxy_pass http://127.0.0.1:2400;
        proxy_redirect off;
	}
}
```
