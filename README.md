# PHP website hosted in nginx and PHP-FPM container with SSL 
[![Build Status](https://travis-ci.org/joemccann/dillinger.svg?branch=master)](https://travis-ci.org/joemccann/dillinger)

Here I'm demonstrating to host a PHP website using nginx and PHP-FPM containers. Also enabled SSL certificate (I'm using self-signed).

## Docker container versions:
- **Nginx** : nginx:alpine

- **PHP-FPM** : php:7.4-fpm-alpine3.15

## Docker installation

```

```
## Creating self-signed certificate with OpenSSL
> Here i'm using a test domain abc.com for crearing the SSL certificate


```bash
$ openssl req -new -newkey rsa:4096 -x509 -sha256 -days 365 -nodes -out nginx-certificate.crt -keyout nginx.key
Generating a RSA private key
........................................++++
................................................................................................................................................................................................................................................................................++++
writing new private key to 'nginx.key'
-----
You are about to be asked to enter information that will be incorporated
into your certificate request.
What you are about to enter is what is called a Distinguished Name or a DN.
There are quite a few fields but you can leave some blank
For some fields there will be a default value,
If you enter '.', the field will be left blank.
-----
Country Name (2 letter code) [AU]:IN
State or Province Name (full name) [Some-State]: Kerala
Locality Name (eg, city) []: 
Organization Name (eg, company) [Internet Widgits Pty Ltd]:
Organizational Unit Name (eg, section) []:
Common Name (e.g. server FQDN or YOUR name) []:abc.com
Email Address []:
```

## Docker compose file

```yml
version: '3'

services:

  php-server:
    image: php:7.4-fpm-alpine3.15
    container_name: php-server
    networks:
      - php-net
    volumes:
      - ./website/:/var/www/html/
    restart: always


  nginx:
    image: nginx:alpine
    container_name: nginx
    networks:
      - php-net
    volumes:
      - ./nginx.conf:/etc/nginx/conf.d/default.conf
      - ./website/:/var/www/html/ 
      - ./cert/nginx-certificate.crt:/etc/nginx/ssl/nginx-certificate.crt 
      - ./cert/nginx.key:/etc/nginx/ssl/nginx.key
    ports:
      - 80:80
      - 443:443
    restart: always

networks:
  php-net:
```

## Run docker compose file 
```bash
$ docker-compose up -d
Creating network "project_web_php-net" with the default driver
Pulling php-server (php:7.4-fpm-alpine3.15)...
7.4-fpm-alpine3.15: Pulling from library/php
59bf1c3509f3: Pull complete
7c7da25b2876: Pull complete
2bc599114627: Pull complete
927a0b37a45a: Pull complete
1766f36e2432: Pull complete
4c13e0891ac6: Pull complete
2cec0bbb4ba9: Pull complete
b36e8fdf0264: Pull complete
387739aab6be: Pull complete
d73f4116d27c: Pull complete
Digest: sha256:6eb4c063a055e144f4de1426b82526f60d393823cb017add32fb85d79f25b62b
Status: Downloaded newer image for php:7.4-fpm-alpine3.15
Pulling nginx (nginx:alpine)...
alpine: Pulling from library/nginx
97518928ae5f: Pull complete
a4e156412037: Pull complete
e0bae2ade5ec: Pull complete
3f3577460f48: Pull complete
e362c27513c3: Pull complete
a2402c2da473: Pull complete
Digest: sha256:12aa12ec4a8ca049537dd486044b966b0ba6cd8890c4c900ccb5e7e630e03df0
Status: Downloaded newer image for nginx:alpine
Creating php-server ... done
Creating nginx      ... done
```
