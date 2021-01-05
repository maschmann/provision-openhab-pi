# install openhab3 on raspberrypi

This is by no means a "secure" setup. Keep it behind a firewall!
Also, this represents _my_ setup: Most stuff is configurable (see defaults/main.yml inside the roles) but basically interdependent.
At the end you'll get:

* openhab3
* influxdb
* grafana (optional)
* phoscon ConBee II (optional)
* a few tweaks for your root/pi users
* [restic backup](https://restic.net/) with cron and helper bash script (see /root/restic-backup)

Add a file ```settings.yml``` to user-settings directory. See .dist file for help.
Replace your ip into the inventory file. 
## copy over your keys

e.g. for linux users: 
```ssh-copy-id -i ~/.ssh/mykey user@host```

## install ansible requirements

```bash
ansible-galaxy install -r requirements.yml
```

## samba share user/password

user: openhab
password: openhab

## enable webserver/proxy

```yml
# user-settings.yml
nginx_enable_webserver: true

influxdb_enable_nginx: true
influxdb_local_domain: .grafana.br0ken.de
influxdb_ssl_cert_privkey: "/etc/letsencrypt/live/br0ken.de/privkey.pem"
influxdb_ssl_cert_fullchain: "/etc/letsencrypt/live/br0ken.de/fullchain.pem"

openhab_enable_nginx: true
openhab_local_domain: .openhab.br0ken.de
openhab_ssl_cert_privkey: "/etc/letsencrypt/live/br0ken.de/privkey.pem"
openhab_ssl_cert_fullchain: "/etc/letsencrypt/live/br0ken.de/fullchain.pem"

phoscon_port: 8090
phoscon_enable_nginx: true
phoscon_local_domain: .deconz.br0ken.de
phoscon_ssl_cert_privkey: "/etc/letsencrypt/live/br0ken.de/privkey.pem"
phoscon_ssl_cert_fullchain: "/etc/letsencrypt/live/br0ken.de/fullchain.pem"

```
This will create default nginx configs for your services. If you remove the ssl_cert entries, only http:80 will be available.
For enabling the configs do a quick:

```bash
cd /etc/nginx/sites-enabled
sudo ln -s ../sites-available/openhab.conf
sudo ln -s ../sites-available/grafana.conf
sudo ln -s ../sites-available/phoscon.conf
systemctl restart nginx
```
## additional openhab bindings

I'm making use of the openhab services feature, where the ```addons.cfg``` will install the desired bindings, etc.
It's a bit rough, but the string values of the variables will just be replaced inside the deployed config file.

```yaml
# user-settings.yml
openhab_install_bindings: "binding = systeminfo,amazonechocontrol,telegram,exec,shelly,pushover,mqtt,hue,deconz"
openhab_install_persistence: "persistence = influxdb,rrd4j"
openhab_install_actions: "action = pushover"
openhab_install_transformations: "transformation = exec,map,jsonpath"
openhab_install_misc: "misc = openhabcloud"
```

## start deployment

```ansible-playbook -i openhab openhab.yml```