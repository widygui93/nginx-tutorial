```
events {

}

http {
	server {
		listen 80;
		server_name nginx-handbook.test;

		root /srv/nginx-handbook-projects/static-demo;
	}
}
```

`root` directive is used for declaring the root directory for a site.

By writing `root /srv/nginx-handbook-projects/static-demo` you're telling NGINX to look for files to serve inside the `/srv/nginx-handbook-projects/static-demo` directory if any request comes to this server. Since NGINX is a web server, it is smart enough to serve the `index.html` file by default.