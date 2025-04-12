# ДЗ к к уроку "Prometheus - Exporters, Service Discovery"

#### Исходное
В инфраструктуре имеется виртуальная машина с установленными на ней сервисами Nginx, Postgresql, Netbox.
На нее установил prometheus-nginx-exporter, prometheus-postgres-exporter, prometheus-blackbox-exporter, prometheus-node-exporter.
На nginx настроил stub_status, в postgres добавил пользователя с соответствующими правами, подправил конфиги экспортеров.
На соседней виртуалке в Docker запустил контейнер с prometheus со следующим конфигом:
prometheus.yml
```
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
