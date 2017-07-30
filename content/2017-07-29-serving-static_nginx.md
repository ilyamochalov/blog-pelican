Title: Serving static content with Nginx
Date: 2017-07-29 22:01
Category: General
Tags: nginx
Summary: This tutorial describes how to use NGINX to serve static content.

#Serving static content with Nginx

Below is an example of app server:

- running on a 8080 port,
- client asks assets at ```/static/``` path,
- assets are located at ```/var/www/example.com/current/static/``` directory,
- you would like to serve assets via Ngnix but proxy other requests to the app server running at 8080 port.

___
    :::nginx
    upstream http_backend {
    server 127.0.0.1:8080;
    }
    
    server {
    listen 80;
    server_name example.com;
    access_log /var/log/example_com_access.log;
    error_log /var/log/example_com_error.log;
    
    location /static/ {
        alias /var/www/example.com/current/static/;
        gzip_static on;
        expires max;
        add_header Cache-Control public;
    }
    
    location / {
        proxy_pass http://http_backend;
        proxy_http_version 1.1;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header Host $http_host;
        proxy_set_header X-Real-IP $remote_addr;
    }
    }
