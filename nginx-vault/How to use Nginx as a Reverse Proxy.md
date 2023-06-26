When configured as a reverse proxy, NGINX sits between the client and a back end server. The client sends requests to NGINX, then NGINX passes the request to the back end.

Once the back end server finishes processing the request, it sends it back to NGINX. In turn, NGINX returns the response to the client.

```c
events {

}

http {

    include /etc/nginx/mime.types;

    server {
        listen 80;
        server_name nginx.test;

        location / {
                proxy_pass "https://nginx.org/";
        }
    }
}
```

Now if you visit http://nginx.test, you'll be greeted by the original [https://nginx.org](https://nginx.org) site while the URI remains unchanged.

So as you can see, at a basic level, the `proxy_pass` directive simply passes a client's request to a third party server and reverse proxies the response to the client.

#### Node.js with Nginx
Now to configure NGINX as a reverse proxy, open your configuration file and update its content as follows:

```c
events {

}
  
http {
    listen 80;
    server_name nginx-handbook.test

    location / {
        proxy_pass http://localhost:3000;
    }
}
```

Now if you send a request to the server from outside you should get a response as follows:
```shell
curl -i http://nginx-handbook.test

# HTTP/1.1 200 OK
# Server: nginx/1.18.0 (Ubuntu)
# Date: Sat, 24 Apr 2021 14:58:01 GMT
# Content-Type: application/json
# Transfer-Encoding: chunked
# Connection: keep-alive

# { "status": "success", "message": "You're reading The NGINX Handbook!" }
```

For example, if your application handles web socket connections, then you should update the configuration as follows:
```c
events {

}
  
http {
    listen 80;
    server_name nginx-handbook.test

    location / {
        proxy_pass http://localhost:3000;
        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection 'upgrade';
    }
}
```

The `proxy_http_version` directive sets the HTTP version for the server. By default it's 1.0, but web socket requires it to be at least 1.1. The `proxy_set_header` directive is used for setting a header on the back-end server. Generic syntax for this directive is as follows:

```conf
proxy_set_header <header name> <header value>
```

So, by writing `proxy_set_header Upgrade $http_upgrade;` you're instructing NGINX to pass the value of the `$http_upgrade` variable as a header named `Upgrade` – same for the `Connection` header.

#### PHP with Nginx
To use FastCGI instead of HTTP, update your configuration as follows:

```c
user www-data;

events {

}

http {

      include /etc/nginx/mime.types;

      server {

          listen 80;
          server_name nginx-handbook.test;
          root /srv/nginx-handbook-projects/php-demo;

          index index.php;

          location / {
              try_files $uri $uri/ =404;
          }

          location ~ \.php$ {
              fastcgi_pass unix:/var/run/php/php7.4-fpm.sock;
              fastcgi_param REQUEST_METHOD $request_method;
              # or
              #include /etc/nginx/fastcgi_params;
              fastcgi_param SCRIPT_FILENAME $realpath_root$fastcgi_script_name;
      }
   }
}
```
Let's begin with the new `index` directive. As you know, NGINX by default looks for an index.html file to serve. But in the demo-project, it's called index.php. So by writing `index index.php`, you're instructing NGINX to use the index.php file as root instead.

As you can see, we've replaced the `proxy_pass` directive by a new `fastcgi_pass`. As the name suggests, it's used to pass a request to a FastCGI service.

The `fastcgi_param REQUEST_METHOD $request_method;` passes the request method to the back-end and the `fastcgi_param SCRIPT_FILENAME $realpath_root$fastcgi_script_name;` line passes the exact location of the PHP script to run.

The `user` directive is responsible for setting the owner for the NGINX worker processes.