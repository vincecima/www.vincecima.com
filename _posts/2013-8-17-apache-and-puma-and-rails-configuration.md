---
title: Apache and Puma and Rails Configuration
layout: post
---

Recently at work we have been experimenting with using [Puma](http://puma.io/) to serve up our Rails applications. While I found plenty of NGINX examples, I had a harder time finding examples for Apache. Below is what I was able to compile from across the web.

Assets that have been combined, minifed and gzipped by the asset pipeline are properly served directly by Apache and all other requests get proxied to Puma. Let [me know on Twitter](https://twitter.com/vincecima) if you have any questions or there is anything I can do better.

{% highlight apacheconf %}
<VirtualHost *:80>
  ServerName example.com
  DocumentRoot /home/www/apps/example/public

  # Redirect all requests that don't match a file on disk under DocumentRoot get proxied to Puma
  RewriteEngine On
  RewriteCond %{DOCUMENT_ROOT}/%{REQUEST_FILENAME} !-f
  RewriteRule ^/(.*)$ http://0.0.0.0:9293%{REQUEST_URI} [P]

  # Don't allow client to fool Puma into thinking connection is secure
  RequestHeader unset X-Forwarded-Proto

  # Anything under public is open to the world
  <Directory /home/www/apps/example/public>
    Require all granted
    Options -MultiViews
  </Directory>

  # Disable ETags (https://github.com/h5bp/server-configs-apache/tree/master/doc#configure-etags)
  # Set Expiration date for all assets to one year in the future
  <LocationMatch "^/assets/.*$">
    Header unset ETag
    FileETag None

    ExpiresActive On
    ExpiresDefault "access plus 1 year"
  </LocationMatch>

  # Rewrite requests for js and css to gzipped versions if client and server support it
  <LocationMatch "^/assets/.*\.(css|js)$">
    RewriteEngine on
    RewriteCond %{HTTP:Accept-Encoding} \b(x-)?gzip\b
    RewriteCond %{REQUEST_FILENAME}.gz -s
    RewriteRule ^(.+)$ $1.gz
  </LocationMatch>

  # Set type and headers for gzipped css
  <LocationMatch "^/assets/.*\.css\.gz$">
    ForceType text/css
    Header set Content-Encoding gzip
    Header add Vary Accept-Encoding
  </LocationMatch>

  # Set type and headers for gzipped js
  <LocationMatch "^/assets/.*\.js\.gz$">
    ForceType application/javascript
    Header set Content-Encoding gzip
    Header add Vary Accept-Encoding
  </LocationMatch>

  # Compress HTML on the fly
  AddOutputFilterByType DEFLATE text/html 

</VirtualHost>

<VirtualHost *:443>
  ServerName example.com
  DocumentRoot /home/www/apps/example/public

  SSLEngine on
  SSLCertificateFile /usr/local/ssl/example.com.crt
  SSLCertificateKeyFile /usr/local/ssl/example.com.key.unsecure

  # Redirect all requests that don't match a file on disk under DocumentRoot get proxied to Puma
  RewriteEngine On
  RewriteCond %{DOCUMENT_ROOT}/%{REQUEST_FILENAME} !-f
  RewriteRule ^/(.*)$ http://0.0.0.0:9293%{REQUEST_URI} [P]

  # Let Puma know that request came in on a secure connection
  RequestHeader set X-Forwarded-Proto "https"

  # Anything under public is open to the world
  <Directory /home/www/apps/example/public>
    Require all granted
    Options -MultiViews
  </Directory>

  # Disable ETags (https://github.com/h5bp/server-configs-apache/tree/master/doc#configure-etags)
  # Set Expiration date for all assets to one year in the future
  <LocationMatch "^/assets/.*$">
    Header unset ETag
    FileETag None

    ExpiresActive On
    ExpiresDefault "access plus 1 year"
  </LocationMatch>

  # Rewrite requests for js and css to gzipped versions if client and server support it
  <LocationMatch "^/assets/.*\.(css|js)$">
    RewriteEngine on
    RewriteCond %{HTTP:Accept-Encoding} \b(x-)?gzip\b
    RewriteCond %{REQUEST_FILENAME}.gz -s
    RewriteRule ^(.+)$ $1.gz
  </LocationMatch>

  # Set type and headers for gzipped css
  <LocationMatch "^/assets/.*\.css\.gz$">
    ForceType text/css
    Header set Content-Encoding gzip
    Header add Vary Accept-Encoding
  </LocationMatch>

  # Set type and headers for gzipped js
  <LocationMatch "^/assets/.*\.js\.gz$">
    ForceType application/javascript
    Header set Content-Encoding gzip
    Header add Vary Accept-Encoding
  </LocationMatch>

  # Compress HTML on the fly
  AddOutputFilterByType DEFLATE text/html 

</VirtualHost>

{% endhighlight %}