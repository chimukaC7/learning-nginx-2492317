# 03_03 Configure NGINX as a Reverse Proxy
-- [Instructor] We've seen how NGINX can be used as a web server for static files. Now let's take a look at two other ways NGINX can be used, as a reverse proxy and a load balancer. I may refer to a reverse proxy as just proxy, but know that I'm referring to the same thing. Proxies and load balancers provide very similar functions. In both cases, a server running as a proxy or load balancer sits between a client and some resource that can fulfill requests from the client. The client connects the proxy or load balancer on the front end. Then the proxy connects to the server or resource on the back end and returns the response to the client. With proxies and load balancers providing what looks like a very similar function, you might be wondering what the difference is between the two. Let's take a closer look. A reverse proxy usually applies to the case where there is only one server on the back end. This could be a web application implemented in Python or Node.js, for example. It could even be another web server running Apache or Tomcat. In these cases, NGINX is great at simplifying things that might be harder to implement in other technologies, like SSL termination and logging. NGINX proxies are also good for accelerating the response from back-end servers by caching content. By storing frequently requested content, NGINX can reduce the load on back-end servers and respond to client requests more quickly. NGINX also has capabilities to compress data, which can reduce the amount of network bandwidth needed to fulfill a request. Acting as a load balancer, NGINX offers the same capabilities as a reverse proxy. But instead of only connecting to one server on the back end, a load balancer connects to multiple servers. This configuration is useful for applications that require more than one back-end server to handle a large number of clients or a high volume of traffic. With more than one server, a site or application can be more reliable by continuing to respond to requests if one server goes down. This also gives site administrators some flexibility for maintenance since they can take one server offline, to update software, for example, without taking the entire site offline. Depending on the needs of the application, as long as one server is online, the site will still be up. Load balancers also support session persistence. This is useful when clients need the same server to accept and respond to all of their requests. Let's consider an example application that requires a client to log in, and the user's account information is stored locally on the server. If the client's requests were routed to a different server each time, they might have to log in over and over again. Instead, session persistence routes all connections to the same server they started on until they log out. Now that we have an understanding of proxying and load balancing, in the next lesson, we'll discuss the upstream directive and how to define the servers that NGINX connects to when


- [Instructor] Now that we've discussed reverse proxies and load balancers, let's take a look at how they're implemented starting with a reverse proxy. The upstream directive, is one of the key components used to configure nginx for proxying and load balancing. The upstream directive groups together servers allowing other directives to reference all of the servers as a single unit you can find more information on the upstream directive in the documentation on nginx.org. If we consider nginx's configuration there are certain contexts where we can apply directives. We can apply them in the global context that affects all of nginx. The HTTP context for items that work with the HTTP protocol and inside the HTTP context the server and location context that specifically configure virtual hosts and the URIs used to access them. Upstreams are defined in the HTTP context. This is useful so that one upstream can be defined and then reused by multiple servers and locations. We'll be connecting to upstreams from location context using the proxy pass directive. Okay, let's go to our development server and configure our first upstream resource. I'm connected to my sandbox server as the root user. I've cloned the exercise files to this system and I'm located in the directory for this lesson. There are two files here, we need to consider. An nginx configuration and a Python script. Let's take a look at the script first. The Python script starts an application server that we can use with the proxy configuration. When the application is accessed it returns the port that was used and the time the request was made we'll configure nginx to connect to this application, using a proxy. Let's see how that gets set up. Now I'm viewing the binaryville.com file. At the top of the configuration, There's an upstream block for app server 7001. I'm including the 7001, just to note the port that the app server is listening on. This isn't a requirement, but just a naming convention. You can use any name you'd like for an upstream block. Inside this block, we can define the servers that are a part of this upstream. In this case, we have one server listening on port 7001. The server statement in an upstream block is different than the server statement used to define a virtual host. In the case of an upstream server, we have to include the DNS name or IP address of the server that this upstream will connect to. We could also use a Unix socket if the backend server is listening on a socket, but in this case the server is another HTTP process running on the same VM as our nginx process. So we'll use the local host IP address, 127.0.0.1 and also indicate the port 7001. Now that we have our upstream defined, we need to connect to it. Near the end of the server block and also near the end of this configuration file, there's a location named /proxy. This location contains a proxy pass directive which belongs to the nginx's HTTP proxy module. If a request comes in on /proxy, nginx will send the traffic into the /proxy location for processing. With the proxy pass directive, the request will be sent to the HTTP resource defined in the upstream named app_server_7001. Any response generated by the upstream is then returned to nginx to be sent back to the client. One important thing to note about the proxy pass configuration is the slash that follows immediately after the name of the upstream. That slash is key to the entire operation. Without the trailing slash, nginx would try to connect to a location named /proxy on the backend server. In most cases, you'll just want a proxy to the root of the backend server and let the application route the request as needed. I'm going to say it again, that slash at the end of the proxy pass is key to the entire operation. Okay, enough talking about this configuration. Let's see how it works. First. I need to put this configuration in place and reload the nginx service. So I'll copy this configuration into /etc/nginx/conf.d. As you might have guessed, I'll test the configuration with nginx-t and then reload nginx with systemctl. And if I can spell it correctly, I will type systemctl reload nginx. Now I'll clear the screen and start the app servers. I can do that by running python3, followed by the name of the script. Okay, the app server is started. Now we can open the browser to see the result. Okay, I'm on the homepage for our test site and I'll update the browser by adding /proxy to the URL. Cool, it works. By hitting the /proxy location on our demo server we're successfully proxying through the nginx service to the app server on port 7001. And if I refresh the page a few times, we can see that the time changes as




The upstream module is one of the key components used to configure NGINX for proxying and load balancing.

You can find more information on the `upstream` and `proxy_pass` directives in the documentation on nginx.org.

[`upstream` directive in ngx_http_upstream_module](https://nginx.org/en/docs/http/ngx_http_upstream_module.html#upstream)

[`Proxy_pass` directive in ngx_http_proxy_module](https://nginx.org/en/docs/http/ngx_http_proxy_module.html#proxy_pass)

[binaryville.conf](./binaryville.conf)

-Upstreams are defined in the HTTP context. This is useful so that one upstream can be defined and then reused by multiple servers also defined in the HTTP context.

```NGINX
upstream app_server_7001 {
    #dns name or ip address
    server 127.0.0.1:7001;
}

server {
    listen 80;
    root /var/www/binaryville;

    server_name binaryville.local www.binaryville.local;
    index index.html index.htm index.php;

    access_log /var/log/nginx/binaryville.local.access.log;
    error_log /var/log/nginx/binaryville.local.error.log;

    location / {
        # First attempt to serve a request as file, then
        # as directory, then fall back to displaying a 404.
        try_files $uri $uri/ =404;
    }

    location /images {
        # Allow the contents of the /image folder to be listed
        autoindex on;

        access_log /var/log/nginx/binaryville.local.images.access.log;
        error_log /var/log/nginx/binaryville.local.images.error.log;
    }

    # specify the page to serve for 404 errors
    error_page 404 /404.html;

    # an exact match location for the 404 page
    location = /404.html {
        # only use this location for internal requests
        internal;
    }

    # specify the page to serve for 500 errors
    error_page 500 502 503 504 /50x.html;

    # an exact match location for the 50x page
    location = /50x.html {
        # only use this location for internal requests
        internal;
    }

    # a location to demonstrate 500 errors
    location /500 {
        fastcgi_pass unix:/this/will/fail;
    }

    # a location to proxy requests to the upstream named 'app_server_7001'
    location /proxy {
        # Trailing slash is key!
        proxy_pass http://app_server_7001/;

        # Logging for access to the proxy
        access_log /var/log/nginx/binaryville.local.proxy.access.log;
        error_log /var/log/nginx/binaryville.local.proxy.error.log;
    }
}
```
