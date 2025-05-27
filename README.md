cat backend.conf
server {
         listen 80;
         server_name dev-backend..io;

       access_log /var/log/nginx/backend-access.log;
       error_log /var/log/nginx/backend-error.log;

       #  if ($http_x_forwarded_proto != "https")
       #    {
       #     rewrite ^(.*)$ https://$server_name$1 permanent;
       #            }

 location / {

          proxy_set_header X-Real-IP $remote_addr;
          proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
          proxy_set_header Host $http_host;
          proxy_set_header X-NginX-Proxy true;
          proxy_pass http://127.0.0.1:3000;
          proxy_redirect off;

         }
 location /socket.io/ {
                proxy_set_header        X-Real-IP       $remote_addr;
                proxy_set_header        X-Forwarded-For $proxy_add_x_forwarded_for;
                proxy_set_header        App-Version     $http_app_version;
                proxy_pass http://127.0.0.1:3005;
                proxy_set_header Upgrade $http_upgrade;
                proxy_set_header Connection "upgrade";
        }
 location /api/log {
                proxy_set_header        X-Real-IP       $remote_addr;
                proxy_set_header        X-Forwarded-For $proxy_add_x_forwarded_for;
                proxy_set_header        App-Version     $http_app_version;
                proxy_pass http://127.0.0.1:3002;
        }
 location /v2/ {
             proxy_set_header        App-Version     $http_app_version;
             proxy_set_header        X-Real-IP       $remote_addr;
             proxy_set_header        X-Forwarded-For $proxy_add_x_forwarded_for;
             proxy_pass http://127.0.0.1:3010/v2/;
        }
  location /api/join-web {
             proxy_set_header        App-Version     $http_app_version;
             proxy_set_header        X-Real-IP       $remote_addr;
             proxy_set_header        X-Forwarded-For $proxy_add_x_forwarded_for;
             proxy_pass http://127.0.0.1:3005/api/join-web;
        }

}


----------------------------------------------------------------------

cat backend01.conf
server {
  listen 443 ssl;
  listen [::]:443 ssl;

  ssl_certificate /etc/ssl/ssl_certs/service.crt;
  ssl_certificate_key /etc/ssl/ssl_certs/service.key;

  server_name dev01-backend.qa.io;
  underscores_in_headers on;

  access_log /var/log/nginx/backend-access02.log;
  error_log /var/log/nginx/backend-error02.log;

  location / {
    proxy_set_header X-Real-IP $remote_addr;
    proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
    proxy_set_header Host $http_host;
    proxy_set_header        App-Version     $http_app_version;
    proxy_set_header X-NginX-Proxy true;
    proxy_pass http://X.X.X.X:3000;
    proxy_redirect off;
  }

  location /socket.io/ {
    proxy_set_header        X-Real-IP       $remote_addr;
    proxy_set_header        X-Forwarded-For $proxy_add_x_forwarded_for;
    proxy_set_header        App-Version     $http_app_version;
    proxy_pass http://X.X.X.X:3005;
    proxy_http_version 1.1;
    proxy_set_header Upgrade $http_upgrade;
    proxy_set_header Connection "upgrade";
  }

  location /api/log {
    proxy_set_header        X-Real-IP       $remote_addr;
    proxy_set_header        X-Forwarded-For $proxy_add_x_forwarded_for;
    proxy_set_header        App-Version     $http_app_version;
    proxy_pass http://X.X.X.X:3101;
  }

  location /v2/ {
    proxy_set_header        App-Version     $http_app_version;
    proxy_set_header        X-Real-IP       $remote_addr;
    proxy_set_header        X-Forwarded-For $proxy_add_x_forwarded_for;
    proxy_pass http://X.X.X.X:3010/v2/;
  }

  location /socket-api/ {
    proxy_set_header        App-Version     $http_app_version;
    proxy_set_header        X-Real-IP       $remote_addr;
    proxy_set_header        X-Forwarded-For $proxy_add_x_forwarded_for;
    proxy_pass http://X.X.X.X:3006/socket-api/;
  }

  location /webrtc-call {
    proxy_set_header        App-Version     $http_app_version;
    proxy_set_header        X-Real-IP       $remote_addr;
    proxy_set_header        X-Forwarded-For $proxy_add_x_forwarded_for;
    proxy_pass http://X.X.X.X:3008/webrtc-call;
    proxy_http_version 1.1;
    proxy_set_header Upgrade $http_upgrade;
    proxy_set_header Connection "upgrade";
  }
  location /signaling-proto {
    proxy_set_header        App-Version     $http_app_version;
    proxy_set_header        X-Real-IP       $remote_addr;
    proxy_set_header        X-Forwarded-For $proxy_add_x_forwarded_for;
    proxy_pass http://X.X.X.X:3008/signaling-proto;
    proxy_http_version 1.1;
    proxy_set_header Upgrade $http_upgrade;
    proxy_set_header Connection "upgrade";
  }
     location /explore {
    proxy_set_header        App-Version     $http_app_version;
    proxy_set_header        X-Real-IP       $remote_addr;
    proxy_set_header        X-Forwarded-For $proxy_add_x_forwarded_for;
    proxy_pass http://X.X.X.X:3011;
    proxy_http_version 1.1;
    proxy_set_header Upgrade $http_upgrade;
    proxy_set_header Connection "upgrade";
  }
       location /api/home/explore {
            rewrite ^/api/home/explore$ /explore/home/explore break;
            proxy_pass http://X.X.X.X:3011;
        }
        location /api/home/explore-views {
            rewrite ^/api/home/explore-views$ /explore/home/explore-views break;
            proxy_pass http://X.X.X.X:3011;
        }
        location /api/news {
         if ($request_method = GET) {
                rewrite ^/api/news$ /explore/news break;
                proxy_pass http://X.X.X.X:3011;
            }
          if ($request_method = POST) {
                proxy_pass http://X.X.X.X:3000;
           }
        }
        location /api/news/fetch {
            rewrite ^/api/news/fetch$ /explore/news/fetch break;
            proxy_pass http://X.X.X.X:3011;
        }
        location /api/marketplace {
            rewrite ^/api/marketplace$ /explore/marketplace break;
            proxy_pass http://X.X.X.X:3011;
        }
        location /api/public {
            rewrite ^/api/public$ /explore/public break;
            proxy_pass http://X.X.X.X:3011;
        }

        location /admin-api/ {
    proxy_set_header        App-Version     $http_app_version;
    proxy_set_header        X-Real-IP       $remote_addr;
    proxy_set_header        X-Forwarded-For $proxy_add_x_forwarded_for;
    proxy_pass http://X.X.X.X1/admin-api/;
    proxy_http_version 1.1;
    proxy_set_header Upgrade $http_upgrade;
    proxy_set_header Connection "upgrade";
  }
}

--------------------------------------------------------------


