# 04_03 Configure allow and deny directives

-- [Instructor] All too often, we hear news about websites getting hacked or otherwise compromised, and the resulting damage that these intrusions can cause. So as you're learning about NGINX, you might be wondering, "What can I do with NGINX to help keep my sites secure?" Here are a few things you should consider when it comes to NGINX and security. The first thing you can do is make sure your operating system and software are using the latest versions and have all security updates applied. This will help make sure you're protected against known vulnerabilities. Next, you can restrict access to certain parts of your site. We'll take a look at how you can configure NGINX to allow access to certain pages based on IP address. You can also use passwords. This can help keep sensitive information protected and slow down intruders. An addition, Transport Layer Security can be used to encrypt transmissions and identify websites. Transport Layer Security, also known as TLS, is an updated, more secure version of the Secure Sockets Layer protocol, also known as SSL. Both of these technologies are used to encrypt the connections between a web server and a browser. This helps keep communications private between the web server and the person requesting the content. Also, using verified SSL certificates can help identify websites and the entity that owns them. Earlier in the course, we discussed installing the latest versions of Ubuntu and NGINX. Now let's take a look at these other ways we can keep our websites secure.



Limiting access is one of the ways we can help secure the contents of a site.  Fortunately, nginx provides the HTTP Access module that includes `allow` and `deny` directives. These are used to limit who is allowed to see certain content and who gets denied.

In NGINX configurations, `allow` and `deny` rules:
- Can be used in the http, server, and location contexts
- Specify patterns to match incoming requests
    - The keyword `all`
    - An IP address: `192.168.1.1`
    - A CIDR block: `192.168.0.0/16`

[Module ngx_http_access_module](https://nginx.org/en/docs/http/ngx_http_access_module.html)

[Classless Inter-Domain Routing (CIDR) Notation](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing)

# Example `allow` and `deny` rules

The following example will:
- Allow requests from the address 192.168.1.1
- Allow requests from all private IP addresses using CIDR notation
- Deny requests from all other addresses

```NGINX
location /admin {
	allow 192.168.1.1;
	allow 10.0.0.0/8;
	allow 172.16.0.0/12;
	allow 192.168.0.0/16;
	deny  all;
}
```

# Rule order
Rules are applied in the order they are defined.

`deny` rules should be placed after `allow` rules.

## Find IP
```
curl https://api.ipify.org
```

