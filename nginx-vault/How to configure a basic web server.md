```
events {

}

http {
	server {
		listen 80;
		server_name nginx-handbook.test;
		return 200 "hello from nginx!\n";
	}
}
```