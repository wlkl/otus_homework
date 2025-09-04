# Проект 
## Мониторинг почтовой системы, состоящей из одного primary сервера на базе iRedMail и двух secondary на базе Proxmox Mail Gateway

Описание работы в презентации.

Первый Alertmanager запущен с доп. параметром --cluster.peer=10.26.12.4:9094, второй соответственно с --cluster.peer=10.25.12.4:9094

VictoriaMetrics запущена с доп. парамертами -retentionPeriod 12 -dedup.minScrapeInterval 60s

VictoriaLogs запущена с доп. параметрами -retentionPeriod 60 -retention.maxDiskUsagePercent=80

В папке Project лежат:

Презентация - otus_project.pptx

Конфиг Prometheus - prometheus.yml

Конфиг Blackbox-exporter - blackbox.yml

Конфиг правил оповещения - blackbox_rules.yml

Конфиг Alertmanager - alertmanager.yml

Конфиг Telegraf - telegraf.conf
