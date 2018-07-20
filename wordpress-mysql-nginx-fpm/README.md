This docker-compose file packs wordpress,nginx,mysql data in one directory, which is good for migrating between hosts.

Initiate local 'storage' with:

```
mkdir -p nginx/
mkdir -p db_data/
mkdir -p logs/nginx/
mkdir -p wordpress/
```

Create `<website>.conf` in `nginx/`:

```
server {
    listen 80;
    server_name _;
 
    root /var/www/html;
    index index.php;
 
    access_log /var/log/nginx/website-access.log;
    error_log /var/log/nginx/website-error.log;
 
	location / {
        try_files $uri $uri/ /index.php?$args;
    
	}
 
	location ~ \.php$ {
        try_files $uri =404;
        fastcgi_split_path_info ^(.+\.php)(/.+)$;
        fastcgi_pass wordpress:9000;
        fastcgi_index index.php;
        include fastcgi_params;
        fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
        fastcgi_param PATH_INFO $fastcgi_path_info;
    
	}

}
```

Good to go!
