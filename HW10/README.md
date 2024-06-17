Задание


Возьмите за основу стенд из занятий по Elasticstack:

Замените filebeat на fluentbeat с аналогичной конфигурацией;
Замените logstash на datapreper он должен принимать данные от fluentbeat;
Замените Elasticsearch и Kibana на Opensearch и Opensearch Dashboard.


1. Создание пользователя opensearch:

groupadd opensearch
useradd opensearch -g opensearch -M -s /bin/bash
passwd opensearch

2. Скачиваем и устанавливаем opensearch:

 wget https://artifacts.opensearch.org/releases/bundle/opensearch/1.2.4/opensearch-1.2.4-linux-x64.tar.gz
 chmod +x opensearch-1.2.4-linux-x64.tar.gz
 tar -xf opensearch-1.2.4-linux-x64.tar.gz
 mkdir /opt/opensearch
 mv ./opensearch-1.2.4/* /opt/opensearch
 rmdir ./opensearch-1.2.4
 chown -R opensearch:opensearch /opt/opensearch
 sudo -u opensearch /opt/opensearch/opensearch-tar-install.sh
 vim /lib/systemd/system/opensearch.service
chown -R root:root /lib/systemd/system/opensearch.service
systemctl daemon-reload
mkdir /var/log/opensearch
chown -R opensearch /var/log/opensearch

3. Запустим настроенный нами демон OpenSearch:

systemctl start opensearch
systemctl status opensearch

4. Проверим работоспособность демона OpenSearch:

curl -X GET https://localhost:9200 -u 'admin:admin' --insecure
    
5. Смена пароля учетной записи OpenSearch:

systemctl stop opensearch
chmod +x /opt/opensearch/plugins/opensearch-security/tools/hash.sh
/opt/opensearch/plugins/opensearch-security/tools/hash.sh
vim /opt/opensearch/plugins/opensearch-security/securityconfig/internal_users.yml

6. Создаем сертификаты:

chmod +x /opt/opensearch/plugins/opensearch-security/tools/securityadmin.sh
cd /opt/opensearch/plugins/opensearch-security/tools
systemctl start opensearch
./securityadmin.sh -cd ../securityconfig/ -icl -nhnv \
   -cacert ../../../config/root-ca.pem \
   -cert ../../../config/kirk.pem \
   -key ../../../config/kirk-key.pem

7. Проверяем работоспособность OpenSearch с новым паролем:

curl -X GET https://localhost:9200 -u 'admin:P@ssw0rd' --insecure

8. Сконфигурирем Opensearch:

vim /opt/opensearch/config/opensearch.yml

9. Скачиваем и устанавливаем OpenSearch-Dashboards:

wget https://artifacts.opensearch.org/releases/bundle/opensearch-dashboards/1.2.0/opensearch-dashboards-1.2.0-linux-x64.tar.gz
chmod +x opensearch-dashboards-1.2.0-linux-x64.tar.gz
tar -xf opensearch-dashboards-1.2.0-linux-x64.tar.gz
mkdir /opt/opensearch-dashboards
mv ./opensearch-dashboards-1.2.0-linux-x64/* ./opensearch-dashboards-1.2.0-linux-x64/.* /opt/opensearch-dashboards/
rmdir ./opensearch-dashboards-1.2.0-linux-x64
chown -R opensearch:opensearch /opt/opensearch-dashboards
vim /opt/opensearch-dashboards/config/opensearch_dashboards.yml
vim /lib/systemd/system/opensearch_dashboards.service
chown -R root:root /lib/systemd/system/opensearch_dashboards.service
systemctl daemon-reload
systemctl start opensearch_dashboards
systemctl status opensearch_dashboards

10. Установка и настройка Fluent-Bit:

yum install -y https://packages.fluentbit.io/centos/7/fluentbit-release-2.0-1.el7.noarch.rpm
yum install -y fluent-bit
vim /etc/fluent-bit/fluent-bit.conf
systemctl start fluent-bit

11. Установка и настройка Data-Prepper:

wget https://artifacts.opensearch.org/data-prepper/2.8.0/opensearch-data-prepper-jdk-2.8.0-linux-x64.tar.gz
tar -xzf opensearch-data-prepper-jdk-2.8.0-linux-x64.tar.gz
cd opensearch-data-prepper-jdk-2.8.0-linux-x64/
vim pipelines/pipelines.yaml
./bin/data-prepper

12. Идем как в Kibana создаем индексы и фильтруем по нужному нам сервису логи.