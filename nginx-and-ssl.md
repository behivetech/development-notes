# Setting up nginx and SSL on a Mac

If you don't have nginx installed already, install it with [homebrew](https://docs.brew.sh/Installation)
```
brew update
brew install nginx
```

In your projects folder create a directory for the certificate setup
```
mkdir -p ~/projects/myserver.local
cd ~/projects/myserver.local
```

This will be a set up to point to a domain locally called myserver.local. Change the name myserver.local
to whatever you'd like to use as a domain locally.

Create a `myserver.conf` file for setting up the cert in the myserver.local folder with the following...

```
[ req ]
default_bits       = 4096
distinguished_name = req_distinguished_name
req_extensions     = req_ext

[ req_distinguished_name ]
countryName                 = US
countryName_default         = US
stateOrProvinceName         = Colorado
stateOrProvinceName_default = Colorado
localityName                = Broomfield
localityName_default        = Broomfield
organizationName            = Rally
organizationName_default    = Newism
commonName                  = myserver.local
commonName_max              = 64
commonName_default          = myserver.local

[ req_ext ]
subjectAltName = @alt_names

[alt_names]
DNS.1   = myserver.local
DNS.2   = localhost
IP.1    = 127.0.0.1 
```

Run the following commands in terminal from the `myserver.local` folder to set up the certificate.
```
openssl genrsa -out private.key 4096
openssl req -new -sha256 -out private.csr -key private.key -config myserver.conf
openssl req -text -noout -in private.csr
openssl x509 -req -days 3650 -in private.csr -signkey private.key -out private.crt -extensions req_ext -extfile myserver.conf
openssl x509 -noout -text -in private.crt
sudo security add-trusted-cert -d -r trustRoot -k /Library/Keychains/System.keychain ./private.crt
mkdir -p /usr/local/etc/ssl/private/
sudo cp private.key /usr/local/etc/ssl/private/self-signed.key
mkdir -p /usr/local/etc/ssl/certs/
sudo cp private.crt /usr/local/etc/ssl/certs/self-signed.crt
```

## Create snippets to use with NGINX
Create the requisite directory:
```
mkdir -p /usr/local/etc/nginx/snippets
```

Create the self-signed.conf snippet:
```
touch /usr/local/etc/nginx/snippets/self-signed.conf
```

Set ssl_certificate and ssl_certificate_key:
```
sudo vim /usr/local/etc/nginx/snippets/self-signed.conf
```

self-signed.conf
```
ssl_certificate /usr/local/etc/ssl/certs/self-signed.crt;
ssl_certificate_key /usr/local/etc/ssl/private/self-signed.key;
```

Create the ssl-params.conf snippet:

```
touch /usr/local/etc/nginx/snippets/ssl-params.conf
sudo vim /usr/local/etc/nginx/snippets/ssl-params.conf
```

ssl-params.conf
```
ssl_protocols TLSv1.1 TLSv1.2;
ssl_prefer_server_ciphers on;
ssl_ciphers "EECDH+AESGCM:EDH+AESGCM:AES256+EECDH:AES256+EDH";
ssl_ecdh_curve secp384r1;
ssl_session_cache shared:SSL:10m;
ssl_session_tickets off;
ssl_stapling on;
ssl_stapling_verify on;
resolver 8.8.8.8 8.8.4.4 valid=300s;
resolver_timeout 5s;
add_header Strict-Transport-Security "max-age=63072000; includeSubdomains";
add_header X-Frame-Options DENY;
add_header X-Content-Type-Options nosniff;
ssl_dhparam /usr/local/etc/ssl/certs/dhparam.pem;
```

## Configure NGINX to use SSL/TLS
Configure nginx.conf:
```
sudo vim /usr/local/etc/nginx/nginx.conf
```

nginx.conf
```
worker_processes  1;
 
events {
   worker_connections  1024;
}
 
http {
   include       mime.types;
   default_type  application/octet-stream;
 
   sendfile        on;
   keepalive_timeout  65;
   proxy_http_version 1.1;
 
   # configure nginx server to redirect to HTTPS
   server {
       listen         80;
       server_name    myserver.local
                      localhost
                      ;
       return 302 https://$server_name:443;
   }
 
   # configure nginx server with ssl
   server {
       listen         443 ssl http2;
       server_name    myserver.local
                      localhost
                      ;
       include snippets/self-signed.conf;
       include snippets/ssl-params.conf;
 
       # route requests to the local development server
       # this is where you set up locations to route to different servers you're running locally
       # the following would point https://myserver.local/api to http://127.0.0.1:3001/
       location /api {
           proxy_pass          http://127.0.0.1:3001/;
       }
       # location /anotherpath {
       #     proxy_pass          http://127.0.0.1:8080/;
       # }

   }
   include servers/*;
}
```

## Setting up your hosts file to point to myserver.local

Edit the /etc/hosts file
```
vim /etc/hosts
```

Add the following line to this file and save it.
```
127.0.0.1 myserver.local
```

## Test and start up NGINX
Check for syntax errors in nginx.conf:
```
sudo nginx -t
```

Restart the NGINX server:
```
sudo nginx -s stop && sudo nginx
```

When starting up nginx, you may see the following warnings which is just saying they didn't come from a certficate authority and is normal...
```
nginx: [warn] "ssl_stapling" ignored, issuer certificate not found for certificate "/usr/local/etc/ssl/certs/self-signed.crt"
nginx: [warn] "ssl_stapling" ignored, issuer certificate not found for certificate "/usr/local/etc/ssl/certs/self-signed.crt"
```

## Helpful references for NGINX and Certificate setup

[Generate ssl certificates with Subject Alt Names on OSX](https://gist.github.com/leevigraham/e70bc5c0a585f40536252abab61875d8)

[How to Create a Self-Signed Certificate for NGINX on macOS](https://nickolaskraus.org/articles/how-to-create-a-self-signed-certificate-for-nginx-on-macos/)

