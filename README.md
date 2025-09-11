# ДЗ к уроку "Prometheus - Exporters, Service Discovery"

В инфраструктуре имеется виртуальная машина с установленными на ней Debian 12 и сервисами Nginx, Postgresql, Netbox.
На нее установил prometheus-nginx-exporter, prometheus-postgres-exporter, prometheus-blackbox-exporter, prometheus-node-exporter.
На nginx настроил stub_status, в postgres добавил пользователя с соответствующими правами, подправил конфиги экспортеров.
На соседней виртуалке запустил prometheus со следующим конфигом:
prometheus.yml
```yaml
global:
  scrape_interval: 15s
scrape_configs:
  - job_name: "host"
    static_configs:
      - targets: ["10.1.12.99:9100"]
  - job_name: "web_server"
    static_configs:
      - targets: ["10.1.12.99:9115"]
  - job_name: "nginx"
    static_configs:
      - targets: ["10.1.12.99:9113"]
  - job_name: "postrgsql"
    static_configs:
      - targets: ["10.1.12.99:9187"]
```
Результат работы:
![изображение](https://github.com/user-attachments/assets/5fabd84f-a7f3-493f-994d-33bcbf97e61d)

# ДЗ к уроку "Отказоустойчивость Prometheus, хранилища метрик для Prometheus"
На ВМ с prometheus установил victoria-metrics, конфигурационного файла нет, создал юнит systemd, в нем запускается бинарник victoria-metrics-prod со следующими параметрами:
```shell
/usr/local/bin/victoria-metrics-prod -storageDataPath=/var/lib/victoria-metrics-data -httpListenAddr=127.0.0.1:8428 -retentionPeriod=2w
```
Добавил в конфиг prometheus remote_write и external_labels:
```yaml
global:
  scrape_interval: 15s
  external_labels:
    site: prod
scrape_configs:
  - job_name: "host"
    static_configs:
      - targets: ["10.1.12.99:9100"]
  - job_name: "web_server"
    static_configs:
      - targets: ["10.1.12.99:9115"]
  - job_name: "nginx"
    static_configs:
      - targets: ["10.1.12.99:9113"]
  - job_name: "postrgsql"
    static_configs:
      - targets: ["10.1.12.99:9187"]
remote_write:
  - url: http://localhost:8428/api/v1/write
```
# ДЗ к уроку "Prometheus, Alertmanager - работа с метриками"
файл конфигурации Alertmanager
```yaml
route:
  group_by: ['alertname']
  group_wait: 30s
  group_interval: 5m
  repeat_interval: 1h
  receiver: 'telegram'
  routes:
  - receiver: "gmail"
    matchers:
    - severity = "warning"
  - receiver: "telegram"
    matchers:
    - severity = "critical"
receivers:
  - name: 'gmail'
    email_configs:
    - to: 'name@yandex.ru'
      from: 'name@gmail.com'
      smarthost: smtp.gmail.com:587
      auth_username: 'name@gmail.com'
      auth_identity: 'name@gmail.com'
      auth_password: 'the_password'
  - name: 'telegram'
    telegram_configs:
    - api_url: https://api.telegram.org
      bot_token: '<bot_token>'
      chat_id: -00000000000
```
# ДЗ к уроку Grafana - продвинутое использование
Grafana установлена на хосте рядом с Prometheus и victoria-metrics. В графане создал две папки App и Infra,
в Infra сделал импорт дашборда для Node Exporter по ID, выбрал источник данных victoria-metrics.
В папке App так же импортировал дашборды для Nginx, Postgresql и Black Box выбрав источник данных victoria-metrics. 

# ДЗ к уроку Алертинг с Kapacitor
На виртуальной машине установил Wordpress, MariaDB, php-fpm, Nginx. Установил InfluxDB v2, создал API Token для Telegraf.
Установил Telegraf, настроил inputs: cpu, disk, net, mem, system, nginx, tail, mysql. 
Донастроил mysql и nginx для сбора статистики телеграфом. Настроил outputs influxdb_v2, прописал token, organization, bucket.
Далее, установил Chronograf и Kapasitor, подключил Chronograf к InfluxDB и Kapasitor. В интерфейсе Chronograf настроил 
дашборды, добавил alerting rule и настроил его на отсылку сообщений в Telegram.

# ДЗ к уроку Beats - инструменты доставки данных
На одной ВМ установленны Wordpress, MariaDB, php-fpm, Nginx, filebeat и metricbeat. На второй ВМ Elasticsearch, kibana
и heartbeat. Конфиги и скриншоты в папке ELK-1.

# ДЗ к уроку Системы агрегации сообщений Logstash/Vector
Установил на вторую ВМ из предыдущего задания Logstash и подключил к Elasticsearch, конфиг Logstash и скрин логов
в папке ELK-2.

# ДЗ к уроку Grafana Loki - управление логами и доставка
На почтовом сервере установил Alloy для сбора логов из maillog, на отдельной ВМ установлены Loki и Grafana.
В папке LOKI-1 лежат конфиг alloy, loki и скрин из Grafana с логами почтаря.

# ДЗ к уроку Graylog
На первой ВМ установлены Wordpress, MariaDB, php-fpm, Nginx. На второй ВМ установлен Graylog, Opensearch, mongodb.
На первую ВМ установил graylog-sidecar, в конфиге прописал адрес Graylog и api_token. В веб интерфейсе Graylog настроил
sidecar добавив отдельный Filebeat Log Collector для сбора логов от каждого приложения. Скрины в папке GRAYLOG.

# ДЗ к уроку Настройка алертинга
Скрипт и скриншоты в папке ZABBIX, ТГ канал - bots_chat