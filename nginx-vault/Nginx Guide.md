How to install Nginx using Vagrant
1. install Vagrant and VirtualBox
2. create [[Vagrantfile]]
3. execute command `vagrant up`
4. execute command `vagrant status`
5. execute command `vagrant ssh nginx-handbook-box`
6. `vagrant halt` (to stop the virtual machine)
7. `vagrant destroy` (to destroy the virtual machine)
8. inside the virtual machine execute command `sudo apt update && sudo apt upgrade -y`
9. execute command `sudo apt install nginx -y`

Reference
[Tutorial Nginx for beginner](https://www.freecodecamp.org/news/the-nginx-handbook/)

How to check Nginx status
```
sudo systemctl status nginx
```

How to start Nginx
```
sudo systemctl start nginx
```

Nginx configure file
```
/etc/nginx/nginx.conf
```

[[How to configure a basic web server]]

How to validate Nginx configuration file
```
sudo nginx -t
```

How to reload Nginx configuration file
```
sudo nginx -s reload
```

or 

```
sudo systemctl restart nginx
```

[[How Nginx know which one of server contexts should handle the request]]

[[How to Serve Static Content using Nginx]]

[[Static file type handling in Nginx]]

[[How to Include Partial Config Files]]

[[Dynamic Routing in Nginx]]

[[Logging in Nginx]]

[[How to use Nginx as a Reverse Proxy]]

[[How to use Nginx as a Load Balancer]]

[[How to optimize Nginx for Maximum Performance]]

[[How to understand the Main configuration file]]