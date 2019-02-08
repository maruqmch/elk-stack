# elk-stack

apt-get install nginx -y
echo "admin:$(openssl passwd -apr1 admin)" | sudo tee -a /etc/nginx/htpasswd.kibana

vi  /etc/nginx/sites-available/kibana
server {
    listen 80 default_server;
    server_name _;
    return 301 https://$server_name$request_uri;
}

server {
    listen 443 default_server ssl http2;
 
    server_name _;
 
    ssl_certificate /etc/ssl/certs/ssl-cert-snakeoil.pem;
    ssl_certificate_key /etc/ssl/private/ssl-cert-snakeoil.key;
    ssl_session_cache shared:SSL:10m;

    auth_basic "Restricted Access";
    auth_basic_user_file /etc/nginx/htpasswd.kibana;
 
    location / {
        proxy_pass http://localhost:5601;
        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection 'upgrade';
        proxy_set_header Host $host;
        proxy_cache_bypass $http_upgrade;
    }
}


 ln -s /etc/nginx/sites-available/kibana /etc/nginx/sites-enabled/kibana
 
 nginx -t
 systemctl restart nginx
 systemctl enable nginx
