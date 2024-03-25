#Задание
Дополнительно установите Alertmanager, настройки алертинг в один из каналов оповещений на ваш выбор (Telegram, email, Slack, etc.);
Создайте набор alert`ов с различными Severity. Для начала ограничтесь warning и critical;
Alertmanager должен уметь отправлять алерты с severity critical в один канал оповещений, в то время как алерты с severity warning в другой;

   1. wget https://github.com/prometheus/alertmanager/releases/download/v0.24.0/alertmanager-0.24.0.linux-amd64.tar.gz
   2. tar xvfz alertmanager-*.tar.gz
   3. mv alertmanager-*/alertmanager /usr/local/bin/
   4. mv alertmanager-*/amtool /usr/local/bin/
   5. mkdir /etc/alertmanager
   6. vim /etc/alertmanager/alertmanager.yml
   7. vim /etc/prometheus/rules.yml
   8. vim /etc/prometheus/test.rules.yml
   9. promtool check rules /etc/prometheus/rules.yml
   10. promtool check rules /etc/prometheus/test.rules.yml
   11. vim /etc/prometheus/prometheus.yml
   12. vim /etc/systemd/system/alertmanager.service
   13. amtool --alertmanager.url=http://localhost:9095/ alert add alertname="test123" severity="test-telegram" job="test-alert" instance="localhost" exporter="none" cluster="test"
