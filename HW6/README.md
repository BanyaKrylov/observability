Задание


На виртуальной машине установите любую open source CMS, которая включает в себя следующие компоненты: nginx, php-fpm, database (MySQL or Postgresql);
На этой же виртуальной машине установите Telegraf для сбора метрик со всех компонентов системы (начиная с VM и заканчивая DB);
На этой же или дополнительной виртуальной машине установите Influxdb, Chronograf, Kapacitor
Настройте отправку метрик в InfluxDB.
Создайте сводный дашборд с самыми на ваш взгляд важными графиками, которые позволяют оценить работоспостобность вашей CMS;
Настройте правила алертинга для черезмерного потребления ресурсов, падения компонентов CMS и 500х ошибок;

    1. yum install https://dl.influxdata.com/telegraf/releases/telegraf-1.20.3-1.x86_64.rpm -y
    2. yum install https://dl.influxdata.com/chronograf/releases/chronograf-1.9.0.x86_64.rpm -y
    3. yum install https://dl.influxdata.com/kapacitor/releases/kapacitor-1.5.3.x86_64.rpm -y
    4. systemctl start influxdb 
    5. influx
    6. CREATE USER "otus" WITH PASSWORD 'password' WITH ALL PRIVILEGES
    7. exit
	8. vim /etc/influxdb/influxdb.conf
    9. systemctl restart influxdb
	10. vim /etc/telegraf/telegraf.conf
    11. systemctl start telegraf
    12. vim /etc/kapacitor/kapacitor.conf
    13. systemctl start kapacitor
    14. systemctl start chronograf
    15. Далее происходила настройка в браузере по адресу 10.3.147.239:8888
    