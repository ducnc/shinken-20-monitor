##Cài thêm module này để hiển thị dữ liệu mà shinken thu được

##Tại host Shinken

`apt-get install pnp4nagios -y`

Sau đó nhập password nagiosadmin là Admin123 hay bất cứ thứ gì bạn thích

```sh
cd /etc/	
cp pnp4nagios/apache.conf apache2/sites-enabled/pnp4nagios.conf
/etc/init.d/apache2 restart

chown -R shinken:shinken /var/spool/pnp4nagios/
chown -R shinken:shinken /usr/lib/pnp4nagios/libexec/process_perfdata.pl
chown -R shinken /var/lib/pnp4nagios
```

`vi /etc/pnp4nagios/npcd.cfg`

    user = shinken
    group = shinken
	
`vi /etc/default/npcd`
    
    RUN="yes"

```sh
su - shinken
shinken install npcdmod
shinken install ui-pnp
cd /etc/shinken
```

`vi modules/npcdmod.cfg`
    
    config_file    /etc/pnp4nagios/npcd.cfg

`vi modules/ui-pnp.cfg`

    uri            http://10.145.34.130/pnp4nagios/

`vi modules/webui.cfg`
	
    modules             auth-cfg-password,sqlitedb,ui-pnp

`vi brokers/broker-master.cfg`

    modules     webui,npcdmod

`vi templates/generic-host.cfg`
	
    process_perf_data               1
    action_url                      ../../pnp4nagios/graph?host=$HOSTNAME$

`vi templates/generic-service.cfg`

    process_perf_data               1                       ; Process performance dat
    action_url                      ../../pnp4nagios/graph?host=$HOSTNAME$&srv=$SERVICEDESC$

```
/etc/init.d/shinken restart
/etc/init.d/npcd restart
update-rc.d -f nagios3 remove
update-rc.d npcd defaults
```
