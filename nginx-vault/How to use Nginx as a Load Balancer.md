After the installation finishes, execute the following commands to start the three Node.js servers:

```shell
pm2 start /srv/nginx-handbook-projects/load-balancer-demo/server-1.js

pm2 start /srv/nginx-handbook-projects/load-balancer-demo/server-2.js

pm2 start /srv/nginx-handbook-projects/load-balancer-demo/server-3.js

pm2 list

# ┌────┬────────────────────┬──────────┬──────┬───────────┬──────────┬──────────┐
# │ id │ name               │ mode     │ ↺    │ status    │ cpu      │ memory   │
# ├────┼────────────────────┼──────────┼──────┼───────────┼──────────┼──────────┤
# │ 0  │ server-1           │ fork     │ 0    │ online    │ 0%       │ 37.4mb   │
# │ 1  │ server-2           │ fork     │ 0    │ online    │ 0%       │ 37.2mb   │
# │ 2  │ server-3           │ fork     │ 0    │ online    │ 0%       │ 37.1mb   │
# └────┴────────────────────┴──────────┴──────┴───────────┴──────────┴──────────┘
```

Now update your configuration as follows:
```c
events {

}

http {

    upstream backend_servers {
        server localhost:3001;
        server localhost:3002;
        server localhost:3003;
    }

    server {

        listen 80;
        server_name nginx-handbook.test;

        location / {
            proxy_pass http://backend_servers;
        }
    }
}
```

The `upstream` context, though, is new. An upstream in NGINX is a collection of servers that can be treated as a single backend.

So the three servers you started using PM2 can be put inside a single upstream and you can let NGINX balance the load between them.