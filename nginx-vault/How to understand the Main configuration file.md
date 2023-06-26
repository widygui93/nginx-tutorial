Now under the virtual hosts settings, you should see two lines as follows:

```c
##
# Virtual Host Configs
##

include /etc/nginx/conf.d/*.conf;
include /etc/nginx/sites-enabled/*;
```

These two lines instruct NGINX to include any configuration files found inside the `/etc/nginx/conf.d/` and `/etc/nginx/sites-enabled/` directories.

There is another directory `/etc/nginx/sites-available/` that's meant to store configuration files for your virtual hosts. The `/etc/nginx/sites-enabled/` directory is meant for storing the symbolic links to the files from the `/etc/nginx/sites-available/` directory.

The idea is to write multiple virtual hosts inside the `/etc/nginx/sites-available/` directory and make some of them active by symbolic linking them to the `/etc/nginx/sites-enabled/` directory.

Create a new file by executing `sudo touch /etc/nginx/sites-available/nginx-handbook` and put the following content in there:

```
server {
    listen 80;
    server_name nginx-handbook.test;

    root /srv/nginx-handbook-projects/static-demo;
}
```

Files inside the `/etc/nginx/sites-available/` directory are meant to be included within the main `http` context so they should contain `server` blocks only.

Now create a symbolic link to this file inside the `/etc/nginx/sites-enabled/` directory by executing the following command:

```shell
sudo ln -s /etc/nginx/sites-available/nginx-handbook /etc/nginx/sites-enabled/nginx-handbook

ls -lh /etc/nginx/sites-enabled/

# lrwxrwxrwx 1 root root 34 Apr 25 08:33 default -> /etc/nginx/sites-available/default
# lrwxrwxrwx 1 root root 41 Apr 25 18:01 nginx-handbook -> /etc/nginx/sites-available/nginx-handbook
```