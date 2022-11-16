# Recursos útils per al .htaccess

**Rewrite i Redirection**

**Forçar a www**

RewriteEngine on\
RewriteCond %{HTTP\_HOST} ^example\\.com \[NC]\
RewriteRule ^(.\*)$ http://www.example.com/$1 \[L,R=301,NC]

**Forçar www de forma genèrica**

RewriteCond %{HTTP\_HOST} !^$\
RewriteCond %{HTTP\_HOST} !^www\\. \[NC]\
RewriteCond %{HTTPS}s ^on(s)|\
RewriteRule ^ http%1://www.%{HTTP\_HOST}%{REQUEST\_URI} \[R=301,L]

**Forçara  no-www**

RewriteEngine on\
RewriteCond %{HTTP\_HOST} ^www\\.example\\.com \[NC]\
RewriteRule ^(.\*)$ http://example.com/$1 \[L,R=301]

**Forçar a  no-www de forma genèrica**

RewriteEngine on\
RewriteCond %{HTTP\_HOST} ^www\\.\
RewriteCond %{HTTPS}s ^on(s)|off\
RewriteCond http%1://%{HTTP\_HOST} ^(https?://)(www\\.)?(.+)$\
RewriteRule ^ %1%3%{REQUEST\_URI} \[R=301,L]

**Forçar a  HTTPS**

RewriteEngine on\
RewriteCond %{HTTPS} !on\
RewriteRule (.\*) https://%{HTTP\_HOST}%{REQUEST\_URI}

**Forçar a  HTTPS darrere un proxy**

RewriteCond %{HTTP:X-Forwarded-Proto} !https\
RewriteRule (.\*) https://%{HTTP\_HOST}%{REQUEST\_URI}

**Forçar un  "trailing slash" (/ per indicar directori)**

RewriteCond %{REQUEST\_URI} /+\[^\\.]+$\
RewriteRule ^(.+\[^/])$ %{REQUEST\_URI}/ \[R=301,L]

**Redirect a pàgina html**&#x20;

Redirect 301 /oldpage.html http://www.yoursite.com/newpage.html\
Redirect 301 /oldpage2.html http://www.yoursite.com/folder/

**Fer alias a un directori**&#x20;

RewriteEngine On\
RewriteRule ^source-directory/(.\*) target-directory/$1

**How to alias paths to script**

RewriteEngine On\
RewriteRule ^$ index.fcgi/ \[QSA,L]\
RewriteCond %{REQUEST\_FILENAME} !-f\
RewriteCond %{REQUEST\_FILENAME} !-d\
RewriteRule ^(.\*)$ index.fcgi/$1 \[QSA,L]

**How to redirect an entire site**

Redirect 301 / http://newsite.com/

**How to alias "Clean" URLs**

RewriteEngine On\
RewriteCond %{SCRIPT\_FILENAME} !-d\
RewriteRule ^(\[^.]+)$ $1.php \[NC,L]

**Security**

**How to deny all access**

\## Apache 2.2\
Deny from all\
\## Apache 2.4\
\# Require all denied

**How deny all access except yours**

\## Apache 2.2\
Order deny,allow\
Deny from all\
Allow from xxx.xxx.xxx.xxx\
\## Apache 2.4\
\# Require all denied\
\# Require ip xxx.xxx.xxx.xxx

**How to allow all access except spammers'**

\## Apache 2.2\
Order deny,allow\
Allow from all\
Deny from xxx.xxx.xxx.xxx\
Deny from xxx.xxx.xxx.xxy\
\## Apache 2.4\
\# Require all granted\
\# Require not ip xxx.xxx.xxx.xxx\
\# Require not ip xxx.xxx.xxx.xxy

**How to deny access to hidden files and directories**

RewriteCond %{SCRIPT\_FILENAME} -d \[OR]\
RewriteCond %{SCRIPT\_FILENAME} -f\
RewriteRule "(^|/)\\." - \[F]\
RedirectMatch 404 /\\..\*$]

**How to deny access to backup and source files**

\<FilesMatch "(\\.(bak|config|dist|fla|inc|ini|log|psd|sh|sql|swp)|\~)$">\
\## Apache 2.2\
Order allow,deny\
Deny from all\
Satisfy All\
\## Apache 2.4\
\# Require all denied\


**How to disable directory browsing**

Options All -Indexes

**How to disable image hotlinking**

RewriteEngine on\
RewriteCond %{HTTP\_REFERER} !^$\
RewriteCond %{HTTP\_REFERER} !^http(s)?://(www\\.)?yourdomain.com \[NC]\
RewriteRule \\.(jpg|jpeg|png|gif)$ - \[NC,F,L]

**How to password protect a directory**

htpasswd -c /home/fellowship/.htpasswd boromir\
AuthType Basic\
AuthName "One does not simply"\
AuthUserFile /home/fellowship/.htpasswd\
Require valid-user

**How to password protect a file or several files**

AuthName "One still does not simply"\
AuthType Basic\
AuthUserFile /home/fellowship/.htpasswd\
\<Files "one-ring.o">\
Require valid-user\
\</Files>\
\<FilesMatch ^((one|two|three)-rings?\\.o)$>\
Require valid-user\
\</FilesMatch>

**Performance**

**Compress Text Files**

```
<IfModule mod_deflate.c>
# Force compression for mangled headers.
# http://developer.yahoo.com/blogs/ydn/posts/2010/12/pushing-beyond-gzipping
<IfModule mod_setenvif.c>
<IfModule mod_headers.c>
SetEnvIfNoCase ^(Accept-EncodXng|X-cept-Encoding|X{15}|~{15}|-{15})$ ^((gzip|deflate)\s*,?\s*)+|[X~-]{4,13}$ HAVE_Accept-Encoding
RequestHeader append Accept-Encoding "gzip,deflate" env=HAVE_Accept-Encoding
</IfModule>
</IfModule>
# Compress all output labeled with one of the following MIME-types
# (for Apache versions below 2.3.7, you don't need to enable `mod_filter`
# and can remove the `<IfModule mod_filter.c>` and `</IfModule>` lines
# as `AddOutputFilterByType` is still in the core directives).
<IfModule mod_filter.c>
AddOutputFilterByType DEFLATE application/atom+xml \
application/javascript \
application/json \
application/rss+xml \
application/vnd.ms-fontobject \
application/x-font-ttf \
application/x-web-app-manifest+json \
application/xhtml+xml \
application/xml \
font/opentype \
image/svg+xml \
image/x-icon \
text/css \
text/html \
text/plain \
text/x-component \
text/xml
</IfModule>
</IfModule>
```

**Set Expires Headers**

By setting expire headers you can leverage browser caching

```
<IfModule mod_expires.c>
ExpiresActive on
ExpiresDefault "access plus 1 month"
# CSS
ExpiresByType text/css "access plus 1 year"
# Data interchange
ExpiresByType application/json "access plus 0 seconds"
ExpiresByType application/xml "access plus 0 seconds"
ExpiresByType text/xml "access plus 0 seconds"
# Favicon (cannot be renamed!)
ExpiresByType image/x-icon "access plus 1 week"
# HTML components (HTCs)
ExpiresByType text/x-component "access plus 1 month"
# HTML
ExpiresByType text/html "access plus 0 seconds"
# JavaScript
ExpiresByType application/javascript "access plus 1 year"
# Manifest files
ExpiresByType application/x-web-app-manifest+json "access plus 0 seconds"
ExpiresByType text/cache-manifest "access plus 0 seconds"
# Media
ExpiresByType audio/ogg "access plus 1 month"
ExpiresByType image/gif "access plus 1 month"
ExpiresByType image/jpeg "access plus 1 month"
ExpiresByType image/png "access plus 1 month"
ExpiresByType video/mp4 "access plus 1 month"
ExpiresByType video/ogg "access plus 1 month"
ExpiresByType video/webm "access plus 1 month"
# Web feeds
ExpiresByType application/atom+xml "access plus 1 hour"
ExpiresByType application/rss+xml "access plus 1 hour"
# Web fonts
ExpiresByType application/font-woff2 "access plus 1 month"
ExpiresByType application/font-woff "access plus 1 month"
ExpiresByType application/vnd.ms-fontobject "access plus 1 month"
ExpiresByType application/x-font-ttf "access plus 1 month"
ExpiresByType font/opentype "access plus 1 month"
ExpiresByType image/svg+xml "access plus 1 month"
</IfModule>
```

**Turn eTags Off**

By omitting ETag, you force to be dependant on Expires headers and cache control\
\<IfModule mod\_headers.c> Header unset ETag \</IfModule> FileETag None

**Miscellaneous**

**How to set PHP variables**

php\_value\
\# For example:\
php\_value upload\_max\_filesize 50M\
php\_value max\_execution\_time 240

**How to custom error pages**

ErrorDocument 500 "Houston, we have a problem."\
ErrorDocument 401 http://error.yourdomain.com/mordor.html\
ErrorDocument 404 /errors/halflife3.html

**How to force downloading**

\<Files \*.md> ForceType application/octet-stream Header set Content-Disposition attachment \</Files>

**How to prevent downloading**

\<FilesMatch "\\.(tex|log|aux)$"> Header set Content-Type text/plain \</FilesMatch>

**How to allow Cross-Domain fonts**

```
<IfModule mod_headers.c>
<FilesMatch "\.(eot|otf|ttc|ttf|woff|woff2)$">
Header set Access-Control-Allow-Origin "*"
</FilesMatch>
</IfModule>
```

**How auto UTF-8 encode**

\# Use UTF-8 encoding for anything served text/plain or text/html AddDefaultCharset utf-8\
\# Force UTF-8 for a number of file formats\
AddCharset utf-8 .atom .css .js .json .rss .vtt .xml

**How to switch to another PHP version**

AddHandler application/x-httpd-php55 .php\
\# Alternatively, you can use AddType\
AddType application/x-httpd-php55 .php
