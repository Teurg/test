
# Настройки Loki

```
x-def-logging: &default-logging
  logging:
    driver: "loki"
    options:
      loki-url: "http://192.168.1.81:3100/loki/api/v1/push"
      loki-batch-size: "100"
      loki-retries: "3"
      loki-max-backoff: "1000ms"
      loki-timeout: "1s"
#      loki-external-labels: "container_name={{.Name}},project=uptime-monitoring" # не уверен, что надо.

```

И в самом docker контейнере добавить (приложения)

<<: *default-logging

А если это Api как с урока то:

api:

  <<: *default-logging	


Установка драйвера (плагина)

```
docker plugin install grafana/loki-docker-driver:latest --alias loki --grant-all-permissions
```