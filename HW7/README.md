Задание


На виртуальной машине установите любую open source CMS, которая включает в себя следующие компоненты: nginx, php-fpm, database (MySQL or Postgresql). Можно взять из предыдущих заданий;
На этой же VM установите filebeat и metricbeat. Filebeat должен собирать логи nginx, php-fpm и базы данных. Metricbeat должен собирать метрики VM, nginx, базы данных;
Установите на второй VM Elasticsearch и kibana, а также heartbeat;
Heartbeat должен проверять доступность следующих ресурсов: веб адрес вашей CMS и порта БД

Задания со звездочкой

Настройте политики ILM так, чтобы логи nginx и базы данных хранились 30 дней, а php-fpm 14 дней;

Настройте в filebeat dissect для логов nginx, так чтобы он переводил access логи в json;


1. Добавление репозитория Elastic

sudo rpm --import https://packages.elastic.co/GPG-KEY-elasticsearch
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

sudo yum install filebeat

3. Настройка Filebeat

sudo vi /etc/filebeat/filebeat.yml

4. Проверка конфигурации

sudo filebeat test config
    
5. Установка Metricbeat

sudo yum install metricbeat

6. Настройка Metricbeat

sudo vi /etc/metricbeat/metricbeat.yml

7. Настройка модулей Metricbeat

sudo metricbeat modules enable system
sudo metricbeat modules enable nginx
sudo metricbeat modules enable postgresql

8. Системный модуль

sudo vi /etc/metricbeat/modules.d/system.yml

9. Nginx модуль

sudo vi /etc/metricbeat/modules.d/nginx.yml

10. PostgreSQL модуль

sudo vi /etc/metricbeat/modules.d/postgresql.yml

11. Проверка конфигурации

sudo metricbeat test config

12. Установка Heartbeat

sudo yum install heartbeat-elastic

13. Настройка Heartbeat

sudo vi /etc/heartbeat/heartbeat.yml

14. Проверка конфигурации

15. sudo heartbeat test config

16. Установка Elasticsearch

sudo yum install elasticsearch

17. Настройка Elasticsearch

sudo vi /etc/elasticsearch/elasticsearch.yml

18. Запуск и включение службы Elasticsearch

sudo systemctl enable elasticsearch
sudo systemctl start elasticsearch

19. Проверка статуса Elasticsearch

sudo systemctl status elasticsearch

20. Установка Kibana

sudo yum install kibana

21. Настройка Kibana

sudo vi /etc/kibana/kibana.yml

22. Запуск и включение службы Kibana

sudo systemctl enable kibana
sudo systemctl start kibana

23. Проверка статуса Kibana

sudo systemctl status kibana

24. Включение и запуск службы Filebeat

sudo systemctl enable filebeat
sudo systemctl start filebeat

25. Проверка статуса Filebeat

sudo systemctl status filebeat

26. Включение и запуск службы Metricbeat

sudo systemctl enable metricbeat
sudo systemctl start metricbeat

27. Проверка статуса Metricbeat

sudo systemctl status metricbeat

28. Включение и запуск службы Heartbeat

sudo systemctl enable heartbeat
sudo systemctl start heartbeat

29. Проверка статуса Heartbeat

sudo systemctl status heartbeat

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






