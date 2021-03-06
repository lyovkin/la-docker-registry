Docker Registry + UI
===========================================

## Requirements
* Docker (Docker version 17.12.0-ce) or higher
* Docker compose (docker-compose version 1.11.2) or higher

Configure custom registry
------------------------- 
- `$ cp docker-compose.override.yml.dist docker-compose.override.yml`
- Now we access our auth/ directory and start configuration our credentials using htpasswd:
- `cd auth && docker run --rm --entrypoint htpasswd registry:2 -Bbn username password > htpasswd`
- Verify:
- `$ cat htpasswd`
- `username:$2y$05$mnaMdOsL7RCjyhTwYnGSp.7OUmZyd2EYLYj0WWKGKSpcVCl9`
- Build docker containers: `docker-compose up -d --build`

Perform a docker login
------------------------
* `docker login -u username http://localhost:5000`

Test publish to Docker Registry
-------------------------------
- Pull example image: `docker pull busybox`
- Tag example image with `localhost:5000` which represents URL of custom Docker Registry: `docker tag busybox localhost:5000/busybox`
- Remove old image: `docker rmi busybox`
- Publish new image to custom Docker Registry `docker push localhost:5000/busybox`

Test pull from Docker Registry
-------------------------------
- Remove pushed image: `docker rmi localhost:5000/busybox`
- Pull pushed image: `docker pull localhost:5000/busybox`

Manage Docker Registry
-------------------------------
- List images via REST API: `http://localhost:5000/v2/_catalog`
- User Web UI: `http://localhost:<port>/`

Delete image
-------------------------------
- Delete via UI: `http://localhost:<port>`
- Run GC command: `docker exec -it registry bin/registry garbage-collect /etc/docker/registry/config.yml`	

Add certs via Certbot
-------------------------------
- `certbot certonly --keep-until-expiring --standalone -d domain.example.com --email info@example.com`
- Rename SSL certificates:
- `cd /etc/letsencrypt/live/domain.example.com/`
- `cp privkey.pem /path/certs/registry.key`
- `cat cert.pem chain.pem > /path/certs/registry.crt`
