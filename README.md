# Nginxfile

[root@ROCKY8 sites-available]# cat ckyc4c..com.conf
# Redirect HTTP to HTTPS
server {
    listen 80;
   # listen [::]:80;
    server_name ckyc4c..com;
    return 301 https://$host$request_uri;
}
 
server {
    listen 443 ssl;
    listen [::]:443 ssl;
    server_name ckyc4c..com;
 
    # SSL Configuration
    ssl_certificate /etc/nginx/ssl/ckyc4c..com.crt;
    ssl_certificate_key /etc/nginx/ssl/ckyc4c..com.key;
    ssl_trusted_certificate /etc/nginx/ssl/ckyc4c..com.CA.crt;
    ssl_protocols TLSv1.2 TLSv1.3;
    ssl_ciphers HIGH:!aNULL:!MD5;
    ssl_prefer_server_ciphers on;
    ssl_session_cache shared:SSL:10m;
    ssl_session_timeout 10m;
    ssl_stapling off;
    ssl_stapling_verify off;
 
    # Hide Nginx Version
    server_tokens off;
 
    # Security Headers
    add_header Strict-Transport-Security "max-age=31536000; includeSubDomains; p                                                                                        reload" always;
    add_header X-Frame-Options "DENY" always;
    add_header X-XSS-Protection "1; mode=block" always;
    add_header X-Content-Type-Options "nosniff" always;
    add_header Referrer-Policy "strict-origin-when-cross-origin" always;
    add_header Permissions-Policy "geolocation=(), microphone=(), camera=()" alw                                                                                        ays;
    add_header Cache-Control "no-store, no-cache, must-revalidate, proxy-revalid                                                                                        ate" always;
    add_header Pragma "no-cache" always;
    add_header Expires 0;
    add_header Content-Security-Policy "frame-ancestors 'none';";
    add_header Content-Type "text/html; charset=UTF-8" always;
    #add_header Content-Type image/svg+xml;
 
    # Disable keep-alive to prevent connection reuse issues
    keepalive_timeout 0;
 
    # Content Security Policy (CSP) for safer resource loading
    add_header Content-Security-Policy "default-src 'self'; script-src 'self' 'n                                                                                        once-RANDOM_NONCE'; style-src 'self'; object-src 'none'; img-src 'self' data:;"                                                                                         always;
 
    # Serve Static Frontend Files
    location / {
        root /var/www/frontend/dist;
        index index.html;
        try_files $uri $uri/ /index.html;
        autoindex off;
    }
 
    # Serve assets properly
    location /assets/ {
        alias /var/www/frontend/dist/assets/;
    }
 
    location /DisKYC_Form/ {
        alias /var/www/frontend/dist/DisKYC_Form/;
        index index.html;
        try_files $uri /index.html;
    }
 
    #location /DisKYC_Form/images/ {
    #    add_header Content-Type image/svg+xml;
    #    try_files &uri =404;
    #}
    # Prevent access to hidden files and backup files
    location ~ /\.(?!well-known).* {
        deny all;
        access_log off;
        log_not_found off;
    }
 
    location ~* \.(bak|backup|zip|tar|gz|old|swp)$ {
        deny all;
        return 403;
    }
 
     #location / {
      #  add_header X-Content-Type-Options nosniff;
    #}
 
    # Improve performance by caching static assets
    #location ~* \.(?:ico|css|js|gif|jpe?g|png|woff2?|eot|ttf|svg|mp4|webp)$ {
    #    expires 30d;
    #    add_header Cache-Control "public, max-age=2592000, immutable";
    #}
 
    # API Proxy Configuration
    location /api/ {
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_cache my_cache;
        proxy_cache_valid 200 10m;
        proxy_cache_valid 404 1m;
        add_header Cache-Control "public, max-age=600";
    }
 
    # Specific API Proxies
    location /api/form/ {
        proxy_pass http://IP:8000/customer-form/;
        proxy_set_header Content-Type "application/json";
    }
 
    location /api/RegisteredCompanies/ {
        proxy_pass http://IP:8002/Registered_companies_get/;
        proxy_set_header Content-Type "application/json";
    }
 
    location /api/company-stats/ {
        proxy_pass http://IP:9007/excel-reading/company-stats;
        proxy_set_header Content-Type "application/json";
    }
 
    location /api/kycbroadcast/ {
        proxy_pass http://IP:8001/customer-form/send-kyc-broadcast;
        proxy_set_header Content-Type "application/json";
    }
 
    location /api/data/ {
        proxy_pass http://IP:8008/responded-nonrespondeddata/nonrespon                                                                                        ded_data;
        proxy_set_header Content-Type "application/json";
    }
 
    location /api/docs/ {
        proxy_pass http://IP:9000/docs;
        proxy_set_header Content-Type "application/json";
    }
 
    location /api/customer_get/ {
        proxy_pass http://IP:8003/customer-get/get;
        proxy_set_header Content-Type "application/json";
    }
 
    location /api/upload_insert_data/ {
        proxy_pass http://IP:9005/customer-form/upload_and_insert_data                                                                                        ;
        proxy_set_header Content-Type "application/json";
    }
 
    location /api/verify_token/ {
        proxy_pass http://IP:8005/form-expiration/verify-token;
        proxy_cache_valid 200 1m;
        proxy_cache_valid 404 1m;
        add_header Cache-Control "public, max-age=60";
    }
 
    location /api/Country/ {
        proxy_pass http://IP:9009/countries_code/countries;
        proxy_set_header Content-Type "application/json; charset=UTF-8";
    }
 
    # Custom Error Pages
    error_page 404 /404.html;
    location = /404.html {
        root /var/www/frontend/dist;
    }
 
    # Logs
    access_log /var/log/nginx/ckyc4c.cloud4c.com.access.log;
    error_log /var/log/nginx/ckyc4c.cloud4c.com.error.log;
}
