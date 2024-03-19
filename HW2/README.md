#Задание
Для Prometheus необходимо установить отдельно хранилище метрик (Victoria Metrics, Grafana Mimir, Thanos, etc.).
Во время записи метрики в хранилище Prometheus должен дополнительно добавлять лейбл site: prod.
Дополнительные параметры хранилища:
Metrics retention - 2 weeks

    1. wget https://github.com/VictoriaMetrics/VictoriaMetrics/releases/download/v1.85.0/victoria-metrics-linux-amd64-v1.85.0.tar.gz
    2. tar zxf victoria-metrics-linux-amd64-*.tar.gz -C /usr/local/bin/
    3. useradd -r -c 'VictoriaMetrics TSDB Service' victoriametrics
    4. mkdir -p /var/lib/victoriametrics /run/victoriametrics
    5. chown victoriametrics:victoriametrics /var/lib/victoriametrics /run/victoriametrics
    6. vim /etc/systemd/system/victoriametrics.service
    7. systemctl daemon-reload
    8. systemctl enable victoriametrics --now
    9. systemctl status victoriametrics
    10. curl 127.0.0.1:8428
    11. vim /etc/prometheus/prometheus.yml
    12. systemctl restart prometheus
