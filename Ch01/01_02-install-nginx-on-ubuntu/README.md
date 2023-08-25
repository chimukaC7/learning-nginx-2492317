# 01_02 Install NGINX on Ubuntu

You can use the cloud provider of your choice but, to follow along with the course, please use Ubuntu as your operating system.

If you need the steps to set up and connect to a server on AWS, there’s a previous lesson with all those details.

1. Connect to a terminal sesson on your server.
1. Switch to the root user

        sudo su -

1. Update the package information for the operating system.

        apt update

1. Install NGINX

        apt install nginx -y

1. Confirm NGINX is installed by checking the version.

        nginx -v

1. Check the status of the NGINX service with the `systemctl` command.

        systemctl status nginx --no-pager


1. Starting,stoping and reloading
-nginx loads configuration into memory
-This command tells NGINX to reload all configuration files from disk. Because NGINX loads its configuration into memory when it first starts, the reload command is needed if any changes are made to the configuration and need to be applied.

        systemctl start nginx
        systemctl stop nginx
        systemctl reload nginx
        systemctl status nginx


1. Testing
        nginx -t
        nginx -T

-The command "nginx -t" checks the configuration without stopping or reloading the service. Using this option, we can make changes to the configuration and then test them before reloading it.


Files and directories

        cd /etc/nginx

-the etc directory holds the congiguration for the entire installation
-the main configuration file is nginx.conf

        cd /var/log/nginx

        cd /var/www
-stores and any other documents that that get served to a client,


-inside nginx.conf
-/etc/nginx/nginx.conf is the main configuration file for NGINX. It contains configuration for the main server program and can load other configurations as well.
-The "include" directive can be used to tell the main configuration file to process configuration files in other directories.
-Settings for NGINX are listed as simple directives on one line or in blocks on multiple lines. Blocks start with a directive and are followed by an open bracket, more directives, and then a closing bracket.

        view nginx.conf


-Standard NGINX configurations use the .conf extension so that they are processed by the include statement in the http directive of /etc/nginx.conf.

-It's good practice to name the vhost or server configuration file after the site defined in the file. Since a single NGINX server can run multiple sites, naming the configuration file the same as the site makes them easier to manage.
