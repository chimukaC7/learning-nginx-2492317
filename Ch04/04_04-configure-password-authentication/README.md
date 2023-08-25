# 04_04 Configure Password Authentication

- [Instructor] Nginx provides a simple method for checking usernames and passwords with the HTTP Auth Basic module. The directives used to configure password authentication are auth_basic and auth_basic_user_file. These directives can be used in the HTTP, server, and location contexts. This is useful, since it allows us the option of locking down entire websites or only certain portions of a site. Let's look at an example using a location context. In this example, the /admin location is configured with basic authentication. Any requests to this location will be met with a prompt to enter a username and password. The auth_basic directive is used to configure the phrase that prompts for authentication. auth_basic can also turn off authentication altogether with the keyword, off. As a side note, not all browsers will include the phrase used in this directive. So, you may see different results depending on the browser used to access a password-protected location. The auth_basic_user_file directive specifies the file where usernames and passwords are stored. In this example, Nginx would use the file/etc/nginx/passwords to validate any input entered into the prompt. The password file follows a simple format of one set of credentials per line, with the username and password separated by a colon. One of the best ways to create and manage password files is with the htpasswd program. You can find the documentation for htpasswd on the apache.org website. Before we go on, I want to add that basic authentication is just that, basic. For many reasons, basic auth doesn't provide a complete solution that we can use to protect modern web applications. If you do use basic authentication, please use it with other Nginx security methods for a more robust security position Okay, with that public service announcement out of the way, let's see how we can use basic auth directives on our demo site. I'm logged into my sandbox server as the root user, and I've cloned the exercise file repo. I'm in the directory with the files for this lesson. Let's take a look at the binaryville configuration. The location serving /private has been updated with basic authentication. So now, along with requesting this location from the correct IP address, I'll also have to enter a username and password. But before we can test that, we need to put this configuration in place, install the htpasswd utility, and create a password file. Back at my terminal prompt, I'll install the Apache 2 utils using the apt install command. This package contains the htpasswd utility. So I'll run apt install-y apache2-utils. With the Apache 2 utils package installed, now I can use the htpasswd command to create a password file. So I'll clear the screen, and type htpasswd. And actually, it's h-t-p-a-s-s-w-d, is the full name of the command. So I've typed h-t-p-a-s-s-w-d, Space, - c/etc/nginx /passwords. And I'm following that with the word, robots, which is the username that I'll be creating a password for. When I run this command, I'm prompted to enter the new password, so I'll enter a super-secret password and press Return. And I'm prompted to confirm this password, so I'll enter it again. Okay, now that I have my password file, let's put the configuration in place, reload Nginx, and check the site. So I will copy binaryville.conf to /etc/ngin/conf.d. Of course, I'm going to test the configuration with nginx -t, and then reload Nginx with systemctl. Okay, the configuration passed, and the service is reloaded, so let's go to the browser. I'm on the homepage for the demo website, and I'll update the URI to browse to /private. Okay, there's the password prompt. Now, I need to enter my credentials. I'll enter robots for the username, my super-secret password, and then I'll click the Sign In button. Success, I was able to log in with the username and password that I configured in the htpasswd file.


Usernames and passwords are an important part of limiting access and keeping things secure online.

NGINX provides the  HTTP Auth Basic module to configure a simple username and password prompt to protect parts of our website.

This module uses the `auth_basic` and `auth_basic_user_file` directives.

[Module ngx_http_auth_basic_module](http://nginx.org/en/docs/http/ngx_http_auth_basic_module.html)

Password authentication:
- Can be used in the http, server, and location contexts
- `auth_basic` prompts for a password or disables authentication with the keyword `off`
- `auth_basic_user_file` specifies the file containing credentials

    File format
    ```
    username:encrypted_password
    ```

Use the `htpasswd` program to create a password file
- Install the `apache2-utils` package to access `htpasswd`

    ```BASH
    sudo apt install -y apache2-utils
    ```

- Create a password file

    ```BASH
    htpasswd -c /path/to/file username
    ```

# Example configuration

```NGINX
location /admin {
 	auth_basic 'Please authenticate...';
  	auth_basic_user_file /etc/nginx/passwords;
 	...
}
```
