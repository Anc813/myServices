DOCS
https://developers.google.com/sheets/api/guides/migration#v4-api_13
https://developers.google.com/sheets/api/reference/rest/v4/spreadsheets.values/get

https://sheets.googleapis.com/v4/spreadsheets/111/values/API!A1:C1000?majorDimension=ROWS&valueRenderOption=FORMATTED_VALUE&key=111


nginx 

```
proxy_cache_path /tmp/nginx_cache levels=1:2 keys_zone=my_cache:10m max_size=100m inactive=60m use_temp_path=off;

server {
    server_name clo-clo.kurs.win; # replace with your domain name

    root /var/www/img; # replace with the path to your files

    location / {
        try_files $uri $uri/ =404;
    }



    # Enable gzip compression
    gzip on;
    gzip_types text/plain application/json application/javascript text/css application/xml text/javascript;
    gzip_proxied any;
    gzip_vary on;
    gzip_min_length 256;


        location /poe.json {
            proxy_pass https://sheets.googleapis.com/v4/spreadsheets/1Xt-dwZdprhsuVrvFuq-GrBgGws-HFMBCT_UBdoCE-ks/values/API!A1:C30?majorDimension=ROWS&valueRenderOption=FORMATTED_VALUE&key=AIzaSyAw6PloH_YgHPl95vPc_NEeQqgUIrtnnJU;
            proxy_set_header Accept-Encoding "";  # Disable gzip to ensure caching
            proxy_cache my_cache;
            proxy_cache_valid 200 10m;  # Cache response for 10 minutes
            proxy_cache_use_stale error timeout invalid_header updating http_500 http_502 http_503 http_504;
            add_header X-Proxy-Cache $upstream_cache_status;
            # Add CORS headers
	    proxy_hide_header 'Access-Control-Allow-Origin';

            add_header 'Access-Control-Allow-Origin' '*' always;
            add_header 'Access-Control-Allow-Methods' 'GET, POST, OPTIONS';
            add_header 'Access-Control-Allow-Headers' 'DNT,User-Agent,X-Requested-With,If-Modified-Since,Cache-Control,Content-Type,Range';
            add_header 'Access-Control-Expose-Headers' 'Content-Length,Content-Range';

            # Optionally, you can add more proxy settings here
            proxy_http_version 1.1;
            proxy_set_header Connection "";
            proxy_ignore_headers Expires Cache-Control Set-Cookie Vary;

	}


    listen 443 ssl; # managed by Certbot
    ssl_certificate /etc/letsencrypt/live/clo-clo.kurs.win/fullchain.pem; # managed by Certbot
    ssl_certificate_key /etc/letsencrypt/live/clo-clo.kurs.win/privkey.pem; # managed by Certbot
    include /etc/letsencrypt/options-ssl-nginx.conf; # managed by Certbot
    ssl_dhparam /etc/letsencrypt/ssl-dhparams.pem; # managed by Certbot

}


server {
    if ($host = clo-clo.kurs.win) {
        return 301 https://$host$request_uri;
    } # managed by Certbot


    listen 80;
    server_name clo-clo.kurs.win;
    return 404; # managed by Certbot


}

```