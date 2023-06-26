#### _listen directive_

```
events {

}

http {
	server {
		listen 80;
		server_name nginx-handbook.test;
		return 200 "hello from port 80!\n";
	}

	server {
		listen 8080;
		server_name nginx-handbook.test;
		return 200 "hello from port 8080!\n";
	}
}
```

Now if you send a request to http://nginx-handbook.test:80 then you'll receive "hello from port 80!" as a response. And if you send a request to http://nginx-handbook.test:8080, you'll receive "hello from port 8080!" as a response:
```
curl nginx-handbook.test:80
# hello from port 80!

curl nginx-handbook.test:8080
# hello from port 8080!
```

#### _server_name directive_

```
events {

}

http {
	server {
		listen 80;
		server_name library.test;
		return 200 "your local library!\n";
	}

	server {
		listen 80;
		server_name librarian.library.test;
		return 200 "welcome dear librarian!\n";
	}
}
```

If you send a request to http://library.test then you'll get "your local library!" as a response. If you send a request to http://librarian.library.test, you'll get "welcome dear librarian!" as a response.

```
curl http://library.test
# your local library!

curl http://librarian.library.test
# welcome dear librarian!
```