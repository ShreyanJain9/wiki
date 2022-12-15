# set up woodpecker ci with gitea on arch linux

assuming you already have a working gitea and are only one person, idk

## install server + agent

```sh
yay -S woodpecker-server woodpecker-agent woodpecker-cli
```

## /etc/woodpecker/server.env

```ini
WOODPECKER_HOST=https://ci.trwnh.com
WOODPECKER_SERVER_ADDR=:9663
WOODPECKER_GRPC_ADDR=:9664
WOODPECKER_ADMIN=a
WOODPECKER_AGENT_SECRET=randomlongstring # openssl rand -hex 32
WOODPECKER_GITEA=true
WOODPECKER_GITEA_URL=https://git.trwnh.com
WOODPECKER_GITEA_CLIENT=  # generate from gitea applications
WOODPECKER_GITEA_SECRET=  # generate from gitea applications
```

use `$WOODPECKER_HOST/authorize` as the redirect uri

## /etc/woodpecker/agent.env

```ini
WOODPECKER_SERVER=localhost:9664
WOODPECKER_AGENT_SECRET=randomlongstring  # same secret as the server.env
```

## /etc/gitea/app.ini

```ini
[webhook]
ALLOWED_HOST_LIST=external,loopback
```

## /etc/nginx/sites/ci.trwnh.com.conf

proxy_pass to `$WOODPECKER_SERVER_ADDR`


```nginx
server {
	server_name ci.trwnh.com
	listen 443 ssl http2;
	listen [::]:443 ssl http2;

	ssl_certificate /etc/letsencrypt/live/trwnh.com/fullchain.pem;
	ssl_certificate_key /etc/letsencrypt/live/trwnh.com/privkey.pem;
	include /etc/letsencrypt/options-ssl-nginx.conf;
	ssl_dhparam /etc/letsencrypt/ssl-dhparams.pem;

	access_log logs/ci.trwnh.com-access.log main;
	error_log logs/ci.trwnh.com-error.log;

	location / {
		proxy_set_header X-Forwarded-For $remote_addr;
		proxy_set_header X-Forwarded-Proto $scheme;
		proxy_set_header Host $http_host;

		proxy_pass http://localhost:9663;
		
		proxy_redirect off;
		proxy_http_version 1.1;
		proxy_buffering off;
		chunked_transfer_encoding off;
	}
}

server {
	server_name ci.trwnh.com;
	listen 80;
	listen [::]:80;
	return 301 https://$host$request_uri;
}
```

## the whole point of this got dam thing

.woodpecker.yml

```yaml
pipeline:
  build:
    image: klakegg/hugo
    commands:
	   - hugo
		- |
		    mkdir $HOME/.ssh
			 echo "$SSH_KEY" > $HOME/.ssh/id_ed25519
			 echo "$SSH_KNOWN_HOSTS" > $HOME/.ssh/known_hosts
			 chown 600 $HOME/.ssh/id_ed25519
			 rsync -avHAX public/ trwnh.com:/srv/http/wiki.trwnh.com/public/
  secrets: [SSH_KEY, SSH_KNOWN_HOSTS]
```

[at this point i give up, it's not worth building on every single push]

[i should have just used rsync directly]

~/.ssh/config
```ssh
Host trwnh.com
	HostName trwnh.com
	Port 22222
```

deploy
```sh
#!/bin/bash
rsync -avz --delete public/ trwnh.com:/srv/http/wiki.trwnh.com/public
```

just do `chmod +x deploy` and now i just deploy with

```sh
hugo
./deploy
```