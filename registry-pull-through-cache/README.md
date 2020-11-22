# registry pull-through cache

This act as a pull-through cache for Docker Hub, after staring up container, use nginx to reverse proxy the `127.0.0.1:5000` port, and make sure there is SSL on the server block.

## Nginx Example

```
server {
    listen 80;
    server_name registry-mirror.nova.moe;
    return 301 https://$host$request_uri;

}

server {
    listen 443 ssl http2;
    listen [::]:443 ssl http2;
    server_name  registry-mirror.nova.moe;

	location / {
	proxy_set_header        Host $host;
	proxy_set_header        X-Real-IP $remote_addr;
	proxy_set_header        X-Forwarded-For $proxy_add_x_forwarded_for;
	proxy_set_header        X-Forwarded-Proto $scheme;
	proxy_pass http://localhost:5000/;

	}
    ssl_certificate /etc/nginx/ssl/xxx.crt;
    ssl_certificate_key /etc/nginx/ssl/xxx.key;
    ssl_session_timeout 1d;
    ssl_session_cache shared:MozSSL:10m;  # about 40000 sessions
    ssl_session_tickets off;

}
```

## Usage

### Temporary Usage

```
docker pull registry-mirror.nova.moe/pingcap/tidb:v4.0.5
docker image tag registry-mirror.nova.moe/pingcap/tidb:v4.0.5 pingcap/tidb:v4.0.5
```

### Persistent Usage

Edit `docker`'s systemd start up script(Use `systemctl status docker` to locate the `.service` file, in Ubuntu it's at `/usr/lib/systemd/system/docker.service`), add `--registry-mirror=https://registry-mirror.nova.moe`, examples as below:

```
...
[Service]
Type=notify
# the default is not to use systemd for cgroups because the delegate issues still
# exists and systemd currently does not support the cgroup feature set required
# for containers run by docker
ExecStart=/usr/bin/dockerd --registry-mirror=https://registry-mirror.nova.moe  -H fd:// --containerd=/run/containerd/containerd.sock
ExecReload=/bin/kill -s HUP $MAINPID
...
```
