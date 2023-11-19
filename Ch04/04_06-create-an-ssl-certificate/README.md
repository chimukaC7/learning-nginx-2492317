# 04_06 Create an SSL certificate

- [Instructor] The hypertext transport protocol, also known as HTTP, is the basic protocol that web servers use to fulfill requests. HTTPS is the secure version of that protocol. HTTPS protects the information being sent between the server and the client by encrypting transmissions. This is important for protecting sites that use sensitive data like credit cards or other personal identifiers. SSL, or secure sockets layer, is the name of the first method developed to encrypt web transmissions. Because SSL can be reversed, using SSL has been deprecated and has fallen out of use. Instead, HTTPS now uses TLS, or transport layer security. So even though TLS is used and favored over SSL, you'll still hear people referring to SSL when they refer to certificates, keys and so on. In fact, NGINX provides the HTTP SSL module for configuring sites to use HTTPS. So let's create a self-signed SSL certificate and then use the directives from the HTTP SSL module to serve our site more securely. But before we go to the terminal, let's take a look at our site in the Chrome browser. Since we're not serving over HTTPS yet, most browsers will let us know by saying something like this site is not secure. Let's see what we can do to fix that.


An SSL certificate is a digital certificate that authenticates a website's identity and helps encrypt the connection between a browser and a web server.

## Create a Self-Signed SSL Certificate
1. Check for the `openssl` command on your system

    ```BASH
    which openssl
    ```

2. Install `openssl` if needed

    ```BASH
    sudo apt install -y openssl
    ```

3. Use the following command to:
    - Create a private key
    - Create a certificate
    - Sign the certificate with the key

    ```
    openssl req -batch -x509 -nodes -days 365 -newkey rsa:2048 \
        -keyout /etc/ssl/private/nginx.key \
        -out /etc/ssl/certs/nginx.crt
    ```
