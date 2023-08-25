# 01_07 Configure a virtual host Part 2

[binaryville.conf](./binaryville.conf)

```NGINX
server {
    listen 80;
    root /var/www/binaryville;
}
```

After the virtual host configuration is finished, the `binaryville.conf` should be as follows:

[binaryville.finished.conf](./binaryville.finished.conf)

```NGINX
server {
    listen 80 default_server;
    root /var/www/binaryville;

    server_name binaryville.local www.binaryville.local;
    index index.html index.htm index.php;
}
```


-The server_name directive lets NGINX know which sites a server configuration applies to. Otherwise, if NGINX were serving multiple sites from the same IP address, the wrong content might be served.
-The default_server directive tells NGINX to use a particular server configuration if no other configurations match the name of the site being requested.
-Because NGINX can serve multiple sites from the same IP address, the default_server statement is used to tell NGINX to use a particular server configuration if no other configurations match the name of the site being requested.