Задание


Установите logstash на VM с Elasticsearch. Перенастройте отправку логов в него. В logstash добавьте парсинг логов при помощи grok фильтр (filebeat пусть при этом шлет сырые данные);
Установите замените logstash вектором с похожим парсингом логов при помощи VRL.



1. Установка Logstash

yum install logstash

2. Настройка отправки логов из Filebeat в Logstash

vim /etc/filebeat/filebeat.yml

3. Настройка Logstash для получения и парсинга логов

vim /etc/logstash/conf.d/logstash.conf

4. Запуск и проверка Logstash

systemctl start logstash
systemctl status logstash
    
5. Перезапуск Filebeat

systemctl restart filebeat

6. Проверка логов Logstash

journalctl -u logstash -f

7. Проверка индексов в Kibana

8. Добавить репозиторий Vector

curl -1sLf 'https://repositories.timber.io/public/vector/cfg/setup/bash.rpm.sh' | sudo bash

9. Установить Vector

yum install vector

10. Настройка Vector

vim /etc/vector/vector.yaml

11. Запуск и проверка Vector

systemctl start vector
systemctl status vector

12. Проверка логов Vector

journalctl -u vector -f

13. Проверка индексов в Kibana



Задания со звездочкой

Настройте dlq в logstash
Настройте политики ILM в logstash при отправке ES

1. Включить DLQ в конфигурации Logstash

vim /etc/logstash/conf.d/logstash.conf

2. Создать ILM политику в Elasticsearch

curl -X PUT "localhost:9200/_ilm/policy/logs_policy" -H 'Content-Type: application/json' -d'
{
  "policy": {
    "phases": {
      "hot": {
        "actions": {
          "rollover": {
            "max_age": "30d",
            "max_size": "50GB"
          }
        }
      },
      "delete": {
        "min_age": "60d",
        "actions": {
          "delete": {}
        }
      }
    }
  }
}
'

3. Создать индексный шаблон в Elasticsearch

curl -X PUT "localhost:9200/_index_template/logs_template" -H 'Content-Type: application/json' -d'
{
  "index_patterns": ["logs-*"],
  "template": {
    "settings": {
      "index.lifecycle.name": "logs_policy",
      "index.lifecycle.rollover_alias": "logs"
    },
    "mappings": {
      "properties": {
        "@timestamp": {
          "type": "date"
        }
      }
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

5. настройть output для Logstash, чтобы он использовал ILM

vim /etc/logstash/conf.d/logstash.conf

6. Перезапуск Filebeat:

systemctl restart filebeat

7. Запуск и проверка Logstash

systemctl start logstash
systemctl status logstash


