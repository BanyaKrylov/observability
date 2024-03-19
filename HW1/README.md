#Задание

На виртуальной машине установите любую open source CMS, которая включает в себя следующие компоненты: nginx, php-fpm, database (MySQL or Postgresql)
На этой же виртуальной машине установите Prometheus exporters для сбора метрик со всех компонентов системы (начиная с VM и заканчивая DB, не забудьте про blackbox exporter, который будет проверять доступность вашей CMS)
На этой же или дополнительной виртуальной машине установите Prometheus, задачей которого будет раз в 5 секунд собирать метрики с экспортеров.

Задание со звездочкой (повышенная сложность)
на VM с установленной CMS слишком много “портов экспортеров торчит наружу” и они доступны всем, попробуйте настроить доступ только по одному и добавить авторизацию.


Prometheus

    1.    useradd --no-create-home --shell /bin/false prometheus
    2.    wget https://github.com/prometheus/prometheus/releases/download/v2.18.1/prometheus-2.18.1.linux-amd64.tar.gz
    3.    tar -xvzf prometheus-2.18.1.linux-amd64.tar.gz
    4.    cp prometheus-2.18.1.linux-amd64/prometheus /usr/local/bin/
    5.    cp prometheus-2.18.1.linux-amd64/promtool /usr/local/bin/
    6.    mkdir /etc/prometheus
    7.    cp -r prometheus-2.18.1.linux-amd64/consoles/ /etc/prometheus/consoles
    8.    cp -r prometheus-2.18.1.linux-amd64/console_libraries/ /etc/prometheus/console_libraries
    9.    cp prometheus-2.18.1.linux-amd64/prometheus.yml /etc/prometheus/
    10.    chown -R prometheus:prometheus /etc/prometheus
    11.    mkdir /var/lib/prometheus
    12.    chown prometheus:prometheus /var/lib/prometheus
    13.    vim /etc/prometheus/prometheus.yml
    14.    vim /etc/systemd/system/prometheus.service
    15.    systemctl daemon-reload
    16.    systemctl start prometheus
    17.    systemctl status prometheus
    18.    systemctl enable prometheus
    19.    curl 'localhost:9090/metrics'

Postgres_exporter

    1. vim /etc/yum.repos.d/prometheus.repo
[prometheus]
name=prometheus
baseurl=https://packagecloud.io/prometheus-rpm/release/el/$releasever/$basearch
repo_gpgcheck=1
enabled=1
gpgkey=https://packagecloud.io/prometheus-rpm/release/gpgkey
       https://raw.githubusercontent.com/lest/prometheus-rpm/master/RPM-GPG-KEY-prometheus-rpm
gpgcheck=1
metadata_expire=300
    2. yum install -y postgres_exporter
    3. psql -U postgres
    4. \c bzsensor
    5. CREATE EXTENSION pg_stat_statements;
    6. systemctl start postgres_exporter

Node_exporter

    1.    useradd --no-create-home --shell /bin/false node_exporter
    2.    wget https://github.com/prometheus/node_exporter/releases/download/v1.0.0/node_exporter-1.0.0.linux-amd64.tar.gz
    3.    tar -xvzf node_exporter-1.0.0.linux-amd64.tar.gz
    4.    cp node_exporter-1.0.0.linux-amd64/node_exporter /usr/local/bin/
    5.    vim /etc/systemd/system/node_exporter.service
    6.    systemctl daemon-reload
    7.    systemctl start node_exporter
    8.    systemctl status node_exporter
    9.    systemctl enable node_exporter
    10.    curl 'localhost:9100/metrics'

Blackbox

    1. mkdir /opt/blackbox
    2. cd /opt/blackbox/
    3. wget https://github.com/prometheus/blackbox_exporter/releases/download/v0.23.0/blackbox_exporter-0.23.0.linux-amd64.tar.gz
    4. tar xzf blackbox_exporter-0.23.0.linux-amd64.tar.gz
    5. cd blackbox_exporter-0.23.0.linux-amd64
    6. cp blackbox_exporter /usr/local/bin/
    7. /etc/systemd/system/blackbox_exporter.service
    8. vim /etc/systemd/system/blackbox_exporter.service
    9. systemctl daemon-reload
    10. vim /opt/blackbox/blackbox.yml
    11. systemctl enable --now blackbox_exporter
    12. systemctl status blackbox_exporter
    13. vim /etc/prometheus/targets.d/blackbox-icmp.yml
    14. vim /etc/prometheus/targets.d/blackbox-tcp.yml
    15. vim /etc/prometheus/targets.d/blackbox-http.yml

Nhinx_exporter

    1. mkdir /opt/nginx-exporter
    2. cd /opt/nginx-exporter
    3. useradd --system --no-create-home --shell /bin/false nginx-exporter
    4. curl -L https://github.com/nginxinc/nginx-prometheus-exporter/releases/download/v0.11.0/nginx-prometheus-exporter_0.11.0_linux_amd64.tar.gz -o nginx-prometheus-exporter_0.11.0_linux_amd64.tar.gz
    5. tar -zxf nginx-prometheus-exporter_0.11.0_linux_amd64.tar.gz
    6. chown -R nginx-exporter:nginx-exporter /opt/nginx-exporter
    7. vim /etc/systemd/system/nginx-exporter.service
    8. systemctl enable nginx-exporter
    9. systemctl start nginx-exporter
    10. systemctl status nginx-exporter
    11. curl localhost:9113/metrics

PHP-PFM_exporter

    1. wget https://github.com/bakins/php-fpm-exporter/releases/download/v0.6.1/php-fpm-exporter.linux.amd64
    2. chmod +x php-fpm-exporter.linux.amd64
    3. cp php-fpm-exporter.linux.amd64 /usr/share/php-fpm-exporter
    4. cd /usr/share/
    5. ./php-fpm-exporter --addr=localhost:9191 --endpoint http://localhost:9001/status >> /dev/null

Объединение кучи портов в один сделал с помощью Nginx, а утентификацию тоже. Для каждого экпортера создал своего пользователя.
