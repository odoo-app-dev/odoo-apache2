# Apache2 ssl configuration for odoo 15

Odoo default port is 8069. You can follow this instruction to convert your http odoo server to a ssl server.
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
