```c
events {

}

http {

    server {

        listen 80;
        server_name nginx-handbook.test;

        location /agatha {
            return 200 "Miss Marple.\nHercule Poirot.\n";
        }
    }
}
```

There can be multiple `location` contexts within a `server` context. 

e.g:

```shell
curl -i http://nginx-handbook.test/agatha

# HTTP/1.1 200 OK
# Server: nginx/1.18.0 (Ubuntu)
# Date: Wed, 21 Apr 2021 15:59:07 GMT
# Content-Type: text/plain
# Content-Length: 29
# Connection: keep-alive

# Miss Marple.
# Hercule Poirot.
```

```shell
curl -i http://nginx-handbook.test/agatha-christie

# HTTP/1.1 200 OK
# Server: nginx/1.18.0 (Ubuntu)
# Date: Wed, 21 Apr 2021 15:59:07 GMT
# Content-Type: text/plain
# Content-Length: 29
# Connection: keep-alive

# Miss Marple.
# Hercule Poirot.
```

This is called a `prefix match` because you're telling NGINX to match any URI starting with "agatha".

For `exact match` will instruct NGINX to respond only if the URL matches exactly.

```c
events {

}

http {

    server {

        listen 80;
        server_name nginx-handbook.test;

        location = /agatha {
            return 200 "Miss Marple.\nHercule Poirot.\n";
        }
    }

}
```

e.g:
```shell
curl -I http://nginx-handbook.test/agatha-christie

# HTTP/1.1 404 Not Found
# Server: nginx/1.18.0 (Ubuntu)
# Date: Wed, 21 Apr 2021 16:14:29 GMT
# Content-Type: text/html
# Content-Length: 162
# Connection: keep-alive

curl -I http://nginx-handbook.test/agatha

# HTTP/1.1 200 OK
# Server: nginx/1.18.0 (Ubuntu)
# Date: Wed, 21 Apr 2021 16:15:04 GMT
# Content-Type: text/plain
# Content-Length: 29
# Connection: keep-alive
```

For `regex match` can check location URLs against complex regular expressions.

```c
events {

}

http {

    server {

        listen 80;
        server_name nginx-handbook.test;

        location ~ /agatha[0-9] {
        	return 200 "Miss Marple.\nHercule Poirot.\n";
        }
    }

}
```

e.g:
```shell
curl -I http://nginx-handbook.test/agatha

# HTTP/1.1 404 Not Found
# Server: nginx/1.18.0 (Ubuntu)
# Date: Wed, 21 Apr 2021 16:14:29 GMT
# Content-Type: text/html
# Content-Length: 162
# Connection: keep-alive

curl -I http://nginx-handbook.test/agatha8

# HTTP/1.1 200 OK
# Server: nginx/1.18.0 (Ubuntu)
# Date: Wed, 21 Apr 2021 16:15:04 GMT
# Content-Type: text/plain
# Content-Length: 29
# Connection: keep-alive
```

A regex match is by default case sensitive
```shell
curl -I http://nginx-handbook.test/Agatha8

# HTTP/1.1 404 Not Found
# Server: nginx/1.18.0 (Ubuntu)
# Date: Wed, 21 Apr 2021 16:14:29 GMT
# Content-Type: text/html
# Content-Length: 162
# Connection: keep-alive
```

To turn this into case insensitive, you'll have to add a `*` after the `~` sign.
```conf
events {

}

http {

    server {

        listen 80;
        server_name nginx-handbook.test;

        location ~* /agatha[0-9] {
        	return 200 "Miss Marple.\nHercule Poirot.\n";
        }
    }

}
```

e.g:
```shell
curl -I http://nginx-handbook.test/agatha8

# HTTP/1.1 200 OK
# Server: nginx/1.18.0 (Ubuntu)
# Date: Wed, 21 Apr 2021 16:15:04 GMT
# Content-Type: text/plain
# Content-Length: 29
# Connection: keep-alive

curl -I http://nginx-handbook.test/Agatha8

# HTTP/1.1 200 OK
# Server: nginx/1.18.0 (Ubuntu)
# Date: Wed, 21 Apr 2021 16:15:04 GMT
# Content-Type: text/plain
# Content-Length: 29
# Connection: keep-alive
```

NGINX assigns priority values to these matches, and a regex match has more priority than a prefix match.

```conf
events {

}

http {

    server {

        listen 80;
        server_name nginx-handbook.test;

		location /Agatha8 {
        	return 200 "prefix matched.\n";
        }
        
        location ~* /agatha[0-9] {
        	return 200 "regex matched.\n";
        }
    }

}
```

e.g: 
```shell
curl -i http://nginx-handbook.test/Agatha8

# HTTP/1.1 200 OK
# Server: nginx/1.18.0 (Ubuntu)
# Date: Thu, 22 Apr 2021 08:08:18 GMT
# Content-Type: text/plain
# Content-Length: 15
# Connection: keep-alive

# regex matched.
```

The final type of match in NGINX is a `preferential prefix match`. To turn a prefix match into a preferential one, you need to include the `^~` modifier before the location URI:
```c
events {

}

http {

    server {

        listen 80;
        server_name nginx-handbook.test;

		location ^~ /Agatha8 {
        	return 200 "prefix matched.\n";
        }
        
        location ~* /agatha[0-9] {
        	return 200 "regex matched.\n";
        }
    }

}
```

Now if you send a request to http://nginx-handbook.test/Agatha8, you'll get the following response:

```shell
curl -i http://nginx-handbook.test/Agatha8

# HTTP/1.1 200 OK
# Server: nginx/1.18.0 (Ubuntu)
# Date: Thu, 22 Apr 2021 08:13:24 GMT
# Content-Type: text/plain
# Content-Length: 16
# Connection: keep-alive

# prefix matched.
```

So the list of all the matches in descending order of priority is as follows

| Match | Modifier |
| --- | --------------|
| Exact | = |
| Preferential Prefix | ^~ |
| REGEX | ~ or ~* |
| Prefix | None |