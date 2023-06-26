```conf
events {

}

http {

    include /etc/nginx/mime.types;

    server {

        listen 80;
        server_name nginx-handbook.test;

        root /srv/nginx-handbook-projects/static-demo;

        try_files $uri $uri/ /not_found;

        location /not_found {
                return 404 "sadly, you've hit a brick wall buddy!\n";
        }
    }
}
```

By writing `try_files $uri $uri/ /not_found;` you're instructing NGINX to try for the requested URI first. If that doesn't work then try for the requested URI as a directory, and whenever NGINX ends up into a directory it automatically starts looking for an index.html file. But if you request a file that doesn't exist, you'll get the response from the `/not_found` location.

