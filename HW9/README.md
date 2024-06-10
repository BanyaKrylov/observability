Задание


В данном ДЗ вам предстоит заменить Elasticsearch на Grafana Loki. Убедитесь, что данные поступают в Grafana Loki и к ним есть доступ.


1. Установка Loki + Promtail

yum install loki promtail -y

2. Настройка Promtail для сбора и отправки логов приложения

 vim /etc/promtail/config.yml

3. Запуск сервисов и проверка ошибок при запуске

systemctl status loki
journalctl -fu loki
systemctl start promtail
journalctl -fu promtail

4. Проверка корректности запуска Loki

Переходим по адресу http://10.3.147.239:3100/metrics и видим метрики
    
5. Проверка корректности запуска Promtail

Переходим по адресу http://10.3.147.239:9080/targets и видим доступные для сбора логов таргеты

6. Проверка сбора и доступности логов

Переходим по адресу Grafana http://10.3.147.239:3000, добавляем новый DataSource и в редакторе дашборда проверяем запросом доступность логов
