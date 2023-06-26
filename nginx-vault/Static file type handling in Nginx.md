```
events {

}

http {
	types {
		text/html html;
		text/css css;
	}

	server {
		listen 80;
		server_name nginx-handbook.test;
		root /srv/nginx-handbook-projects/static-demo;
	}
}
```