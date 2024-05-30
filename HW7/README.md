Задание


На виртуальной машине установите любую open source CMS, которая включает в себя следующие компоненты: nginx, php-fpm, database (MySQL or Postgresql). Можно взять из предыдущих заданий;
На этой же VM установите filebeat и metricbeat. Filebeat должен собирать логи nginx, php-fpm и базы данных. Metricbeat должен собирать метрики VM, nginx, базы данных;
Установите на второй VM Elasticsearch и kibana, а также heartbeat;
Heartbeat должен проверять доступность следующих ресурсов: веб адрес вашей CMS и порта БД


1. Добавление репозитория Elastic

rpm --import https://packages.elastic.co/GPG-KEY-elasticsearch
cat <<EOF | sudo tee /etc/yum.repos.d/elastic.repo
[elastic-7.x]
name=Elastic repository for 7.x packages
baseurl=https://artifacts.elastic.co/packages/7.x/yum
gpgcheck=1
gpgkey=https://artifacts.elastic.co/GPG-KEY-elasticsearch
enabled=1
autorefresh=1
type=rpm-md
EOF

2. Установка Filebeat

yum install filebeat

3. Настройка Filebeat

vim /etc/filebeat/filebeat.yml

4. Проверка конфигурации

filebeat test config
    
5. Установка Metricbeat

yum install metricbeat

6. Настройка Metricbeat

vim /etc/metricbeat/metricbeat.yml

7. Настройка модулей Metricbeat

metricbeat modules enable system
metricbeat modules enable nginx
metricbeat modules enable postgresql

8. Системный модуль

vim /etc/metricbeat/modules.d/system.yml

9. Nginx модуль

vim /etc/metricbeat/modules.d/nginx.yml

10. PostgreSQL модуль

vim /etc/metricbeat/modules.d/postgresql.yml

11. Проверка конфигурации

metricbeat test config

12. Установка Heartbeat

yum install heartbeat-elastic

13. Настройка Heartbeat

vim /etc/heartbeat/heartbeat.yml

14. Проверка конфигурации

15. heartbeat test config

16. Установка Elasticsearch

yum install elasticsearch

17. Настройка Elasticsearch

vim /etc/elasticsearch/elasticsearch.yml

18. Запуск и включение службы Elasticsearch

systemctl enable elasticsearch
systemctl start elasticsearch

19. Проверка статуса Elasticsearch

systemctl status elasticsearch

20. Установка Kibana

yum install kibana

21. Настройка Kibana

vim /etc/kibana/kibana.yml

22. Запуск и включение службы Kibana

systemctl enable kibana
systemctl start kibana

23. Проверка статуса Kibana

systemctl status kibana

24. Включение и запуск службы Filebeat

systemctl enable filebeat
systemctl start filebeat

25. Проверка статуса Filebeat

systemctl status filebeat

26. Включение и запуск службы Metricbeat

systemctl enable metricbeat
systemctl start metricbeat

27. Проверка статуса Metricbeat

systemctl status metricbeat

28. Включение и запуск службы Heartbeat

systemctl enable heartbeat
systemctl start heartbeat

29. Проверка статуса Heartbeat

systemctl status heartbeat

30. Настройка индексов для метрик

После запуска Kibana, открываем браузер и переходим по адресу http://<your-server-ip>:5601. Выполним следующие шаги для настройки индексов в Kibana:

Перейти в раздел "Management":

Нажать на иконку меню (слева внизу) и выберать "Management".
Настройка индексов:

В разделе "Stack Management" выбраем "Index Patterns".
Нажимаем "Create index pattern".
Вводим название индекса (например, filebeat-*, metricbeat-*, heartbeat-*) и нажимаем "Next step".
Выбераем поле временной метки (@timestamp) и нажимаем "Create index pattern".
Повторяем шаги для всех нужных индексов (filebeat-*, metricbeat-*, heartbeat-*).

31. Проверка и настройка индексов 

metricbeat setup --template -E output.elasticsearch.hosts=["localhost:9200"]
filebeat setup --template -E output.elasticsearch.hosts=["localhost:9200"]
heartbeat setup --template -E output.elasticsearch.hosts=["localhost:9200"]


Задания со звездочкой

Настройте политики ILM так, чтобы логи nginx и базы данных хранились 30 дней, а php-fpm 14 дней;

1. Политика для логов Nginx и баз данных (30 дней):

curl -X PUT "localhost:9200/_ilm/policy/nginx_db_policy" -H 'Content-Type: application/json' -d'
{
  "policy": {
    "phases": {
      "hot": {
        "actions": {
          "rollover": {
            "max_age": "30d"
          }
        }
      },
      "delete": {
        "min_age": "30d",
        "actions": {
          "delete": {}
        }
      }
    }
  }
}
'

2. Политика для логов PHP-FPM (14 дней):

curl -X PUT "localhost:9200/_ilm/policy/php_fpm_policy" -H 'Content-Type: application/json' -d'
{
  "policy": {
    "phases": {
      "hot": {
        "actions": {
          "rollover": {
            "max_age": "14d"
          }
        }
      },
      "delete": {
        "min_age": "14d",
        "actions": {
          "delete": {}
        }
      }
    }
  }
}
'

3. Шаблон индекса для логов Nginx и баз данных:

curl -X PUT "localhost:9200/_index_template/nginx_db_template" -H 'Content-Type: application/json' -d'
{
  "index_patterns": ["nginx-*", "db-*"],
  "template": {
    "settings": {
      "index.lifecycle.name": "nginx_db_policy",
      "index.lifecycle.rollover_alias": "nginx_db"
    }
  }
}
'

4. Шаблон индекса для логов PHP-FPM:

curl -X PUT "localhost:9200/_index_template/php_fpm_template" -H 'Content-Type: application/json' -d'
{
  "index_patterns": ["php-fpm-*"],
  "template": {
    "settings": {
      "index.lifecycle.name": "php_fpm_policy",
      "index.lifecycle.rollover_alias": "php_fpm"
    }
  }
}
'

5. Настройка Filebeat для использования шаблонов индексов и политик ILM:

vim /etc/filebeat/filebeat.yml

6. Перезапуск Filebeat:

systemctl restart filebeat

7. Проверка индексов и политик ILM:

curl -X GET "localhost:9200/_cat/indices?v"
curl -X GET "localhost:9200/_ilm/policy"

8. Проверка состояния индексов:

curl -X GET "localhost:9200/_ilm/explain/nginx-*"
curl -X GET "localhost:9200/_ilm/explain/php-fpm-*"

Настройте в filebeat dissect для логов nginx, так чтобы он переводил access логи в json;

1. Настройка Filebeat для чтения логов Nginx и использования dissect-процессора для разбора логов

vim /etc/filebeat/filebeat.yml

2. Перезапуск Filebeat

systemctl restart filebeat

3. Проверка данных в Elasticsearch

curl -X GET "localhost:9200/filebeat-*/_search?pretty" -H 'Content-Type: application/json' -d'
{
  "_source": ["client_ip", "ident", "user", "timestamp", "method", "request", "http_version", "status_code", "bytes", "referrer", "user_agent"],
  "query": {
    "match_all": {}
  }
}
'


