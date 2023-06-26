By default, NGINX's log files are located inside `/var/log/nginx`.
There are `access.log` and `error.log`

Now to make an entry in the access log, send a request to the server.
```
curl -I http://nginx-handbook.test

# HTTP/1.1 200 OK
# Server: nginx/1.18.0 (Ubuntu)
# Date: Sun, 25 Apr 2021 08:35:59 GMT
# Content-Type: text/html
# Content-Length: 960
# Last-Modified: Sun, 25 Apr 2021 08:35:33 GMT
# Connection: keep-alive
# ETag: "608529d5-3c0"
# Accept-Ranges: bytes

sudo cat /var/log/nginx/access.log 

# 192.168.20.20 - - [25/Apr/2021:08:35:59 +0000] "HEAD / HTTP/1.1" 200 0 "-" "curl/7.68.0"
```

Any request to the server will be logged to this file by default. But we can change this behavior using the `access_log` directive.

```c
events {

}

http {

    include /etc/nginx/mime.types;

    server {

        listen 80;
        server_name nginx-handbook.test;
        
        location / {
            return 200 "this will be logged to the default file.\n";
        }
        
        location = /admin {
            access_log /var/logs/nginx/admin.log;
            
            return 200 "this will be logged in a separate file.\n";
        }
        
        location = /no_logging {
            access_log off;
            
            return 200 "this will not be logged.\n";
        }
    }
}
```

The error.log file, on the other hand, holds the failure logs.

```shell
sudo nginx -s reload

# nginx: [emerg] invalid number of arguments in "return" directive in /etc/nginx/nginx.conf:14
```

Check the content of the error log and the message should be present there as well:

```shell
sudo cat /var/log/nginx/error.log 

# 2021/04/25 08:35:45 [notice] 4169#4169: signal process started
# 2021/04/25 10:03:18 [emerg] 8434#8434: invalid number of arguments in "return" directive in /etc/nginx/nginx.conf:14
```

There are eight levels of error messages:

- `debug` – Useful debugging information to help determine where the problem lies.
- `info` – Informational messages that aren't necessary to read but may be good to know.
- `notice` – Something normal happened that is worth noting.
- `warn` – Something unexpected happened, however is not a cause for concern.
- `error` – Something was unsuccessful.
- `crit` – There are problems that need to be critically addressed.
- `alert` – Prompt action is required.
- `emerg` – The system is in an unusable state and requires immediate attention

You can override this behavior using the `error_log` directive. If you want to set the minimum level of a message to be `warn`, then update your configuration file as follows:

```c
events {

}

http {

    include /etc/nginx/mime.types;

    server {

        listen 80;
        server_name nginx-handbook.test;
	
    	error_log /var/log/error.log warn;

        return 200 "..." "...";
    }

}
```

Unlike the previous output, there are no `notice` entries here. `emerg` is a higher level error than `warn` and that's why it has been logged.