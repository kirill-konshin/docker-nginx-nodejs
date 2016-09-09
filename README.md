Docker Nginx + NodeJS
=====================

By-the-book image based on offcial Docker Nginx with added NodeJS.

This package is should be used with a Single Page App build which can be served statically by Nginx. The build can be produced by any JS build tool like Webpack, Browserify, Grunt, Gulp, etc. 

Suggested usage
---------------

Assume you have at least the following files in your repository:

- `package.json`
- `nginx.conf`

```dockerfile
FROM kirillkonshin/nginx-nodejs
RUN mkdir -p /usr/share/nginx/html
ADD ./package.json /usr/share/nginx/html
WORKDIR /usr/share/nginx/html
RUN npm install
ADD . /usr/share/nginx/html
COPY ./nginx.conf /etc/nginx/nginx.conf
CMD <YOUR-BUILD-COMMAND> && nginx -g 'daemon off;'
```

Replace `<YOUR-BUILD-COMMAND>` with something like `webpack --progress` or anything else.

Sample `nginx.conf`:

```nginx
user  nginx;
worker_processes  1;
error_log /var/log/nginx/error.log warn;
pid /var/run/nginx.pid;
events {
    worker_connections 1024;
}
http {
    include /etc/nginx/mime.types;
    default_type application/octet-stream;
    log_format  main  '$remote_addr - $remote_user [$time_local] "$request" '
                      '$status $body_bytes_sent "$http_referer" '
                      '"$http_user_agent" "$http_x_forwarded_for"';
    access_log /var/log/nginx/access.log main;
    sendfile on;
    keepalive_timeout 65;   
    gzip on;
    server {
        listen 80;
        server_name _ localhost 127.0.0.1 0.0.0.0;
        root /usr/share/nginx/html/build/web;
    	location / {
            try_files $uri $uri/ /index.html?$args;
        }
    }
}
```