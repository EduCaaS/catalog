#Installation

##Setup nginx as a reverse proxy

Since the docker container we use as a Collabora Online server doesn't come with valid certificates for your domain, we use nginx as a reverse proxy which will provide us with a valid ssl setup to connect our browser to.

You should run this reverse proxy on a separate (sub-)domain, e.g. office.example.com, different from Nextcloud domain, e.g. cloud.example.com.

Add this server block to the proxy container running NGINX:

````
server {
      listen       443;
      server_name  office.example.com;

#ssl_certificate /path/to/certficate; # not needed as long as we use Letsencrypt certs
#ssl_certificate_key /path/to/key; # idem

# static files
location ^~ /loleaflet {
    proxy_pass https://code:9980;
    proxy_set_header Host $http_host;
}

# WOPI discovery URL
location ^~ /hosting/discovery {
    proxy_pass https://code:9980;
    proxy_set_header Host $http_host;
}

# websockets, download, presentation and image upload
location ^~ /lool {
    proxy_pass https://code:9980;
    proxy_set_header Upgrade $http_upgrade;
    proxy_set_header Connection "upgrade";
    proxy_set_header Host $http_host;
}
}
````
And reload your nginx config using sudo nginx -s reload.

##Install the Nextcloud app

Go to the Apps section and choose "Productivity"
Install the "Collabora Online" app
In Admin -> Collabora Online specific the URL of the server you have setup before (https://office.example.com), without the end slash.

Enjoy

Now you can open the "Collabora Online" app and edit all your documents right in your browser.
