Задание


Для успешного выполнения ДЗ вам нужно сконфигурировать hearthbeat, filebeat и metricbeat на отправку данных в elasticsearch:

На виртуальной машине установите любую open source CMS, которая включает в себя следующие компоненты: nginx, php-fpm, database (MySQL or Postgresql). Можно взять из предыдущих заданий;
На этой же VM установите graylog sidecar.
Установите на второй VM Graylog (server, opensearch, mongodb) и datapreper
Подключите установленный sidecar в graylog и настройте сбор и отправку логов nginx, php-fpm и базы данных, используя sidecar.
Разделите логи в разные стримы.

В качестве результата создайте репозиторий приложите конфиги datapreper, sidecar.

Приложите скриншот полученных данных отображенных в Graylog.


1. Установка и настройка Graylog:

yum install https://packages.graylog2.org/repo/packages/graylog-4.0-repository_latest.rpm
yum install graylog-server graylog-enterprise-plugins graylog-integrations-plugins graylog-enterprise-integrations-plugins
pwgen -N 1 -s 96
echo -n P@ssw0rd | sha256sum
pwgen -N 1 -s 96
echo -n P@ssw0rd | sha256sum
vim /etc/graylog/server/server.conf

2. Установка и настройка Graylog Sidecar:

rpm -Uvh https://packages.graylog2.org/repo/packages/graylog-sidecar-repository-1-2.noarch.rpm
yum -y install graylog-sidecar
vim /etc/graylog/sidecar/sidecar.yml

3. Настройка Graylog в Web

Создаем Inputs
Конфигурируем Sidecar
Создаем Stream

Разворачивал на Elasticsearch + Logstash + Filebeat. Не стал через opensearch и datapreper, т.к. для меня они неудобны. 