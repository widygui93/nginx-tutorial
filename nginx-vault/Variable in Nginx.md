```conf
set $<variable_name> <variable_value>;

# set name "Farhan"
# set age 25
# set is_working true
```

update the configuration as follows:

```conf
events {

}

http {

    server {

        listen 80;
        server_name nginx-handbook.test;

        return 200 "Host - $host\nURI - $uri\nArgs - $args\n";
    }

}
```

e.g:
```shell
# curl http://nginx-handbook.test/user?name=Farhan

# Host - nginx-handbook.test
# URI - /user
# Args - name=Farhan
```

you can access the individual values using the `$arg` variable.

```conf
events {

}

http {

    server {

        listen 80;
        server_name nginx-handbook.test;
        
        set $name $arg_name; # $arg_<query string name>

        return 200 "Name - $name\n";
    }

}
```

e.g:

```shell
curl http://nginx-handbook.test?name=Farhan

# Name - Farhan
```