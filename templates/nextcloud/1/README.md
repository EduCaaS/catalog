##Setup nginx as a reverse proxy

Since the docker container we use as a Collabora Online server doesn't come with valid certificates for your domain, we use nginx as a reverse proxy which will provide us with a valid ssl setup to connect our browser to.

You can either run this reverse proxy on a seperate (sub-)domain or add it to the same domain your Nextcloud runs on.

Add a new server block to your nginx config or add the location entries to an existing one if you're re-using the same domain.

````server {
      listen       443 ssl;
      server_name  office.example.com;

      #ssl_certificate /path/to/certficate;
      #ssl_certificate_key /path/to/key;

      # static files
      location ^~ /loleaflet {
          proxy_pass https://localhost:9980;
          proxy_set_header Host $http_host;
      }

      # WOPI discovery URL
      location ^~ /hosting/discovery {
          proxy_pass https://localhost:9980;
          proxy_set_header Host $http_host;
      }

      # websockets, download, presentation and image upload
      location ^~ /lool {
          proxy_pass https://localhost:9980;
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
In Admin -> Collabora Online specific the server you have setup before (https://office.example.com)
Enjoy

Now you can open the "Collabora Online" app and edit all your documents right in your browser.
