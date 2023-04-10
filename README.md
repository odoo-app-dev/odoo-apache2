# Apache2 ssl configuration for odoo 15

Odoo default port is 8069. You can follow this instruction to convert your http odoo server to a ssl server.
In case you change the workers on odoo, you need to redirect /longpolling in the apache config file. Otherwise, 
odoo could not access to longpolling address and the bus would not work.

#### Prerequisits:
1. Linix server ( Ubunto, Debian )
2. Apache2 installed
3. odoo 15 installed
4. you need a registerd web domain 
5. you need to buy a ssl certificate or setup let'sEncrypt one 

### a) odoo config
- edit the /etc/odoo/odoo.conf
```
http_port = 8069
longpolling_port = 8072
proxy_mode = True 
workers = 4
```
- reboot odoo
```
systemctl restart odoo
```

### b) Install apache extentions
on your linux server:
```
a2enmod proxy
a2enmod proxy_http 
a2enmod ssl
```
### c) Redirect http to http
- edit /etc/apache2/site-available/<youDomain.com>.conf
```
<VirtualHost *:80>
    ServerName <yourDomain.com>
    Redirect permanent / https://<yourDomain.com>/
</VirtualHost>

<VirtualHost *:443>
    Servername <yourDomain.com>
    SSLEngine on
    Include /etc/letsencrypt/options-ssl-apache.conf
    SSLCertificateKeyFile /etc/ssl/<yourDomain.com>/privkey.pem
    SSLCertificateFile  /etc/ssl/<yourDomain.com>/fullchain.pem
    SSLProxyEngine on
    RequestHeader set "X-Forwarded-Proto" "https"
    SetEnv proxy-nokeepalive 1
    
    ProxyPass /longpolling http://0.0.0.0:8072/longpolling
    ProxyPassReverse /longpolling http://0.0.0.0:8072/longpolling
    
    ProxyPass / http://<yourDomain.com>:8069/
    ProxyPassReverse / http://<yourDomain.com>:8069/
    

    ProxyErrorOverride off
    TransferLog /var/log/apache2/transfer.erp.your-domain.at.log
    #Fix IE problem (httpapache proxy dav error 408/409)
    SetEnv proxy-nokeepalive 1
</VirtualHost>

```
- Reboot apache server to apply changes:
```
systemctl restart apache2
```
