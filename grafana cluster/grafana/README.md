## Копируем с контейнера конфиг

```
sudo docker cp 8843ebaa10ef:/etc/grafana/grafana.ini ./
```

Меняем в секции database адрес до базы данных

```
type = postgres
host = 192.168.1.81:5432
name = grafana
user = grafana_user
password = "grafanapasswd"
```

Сохраняем и делаем

```
docker compose down
```

Правим compose.yml и раскоментируем volumes


## Установка nginx для балансировки

```
sudo apt update && sudo apt install -y nginx
```

Удаляем дефолтный конфиг

```
sudo rm /etc/nginx/sites-enabled/default
```

Создаем новый конфиг

```
sudo nano /etc/nginx/sites-enabled/grafana.conf
```

Конфиг grafana.conf

```
upstream grafana-backend {
  server 192.168.1.81:3000 weight=1;
  server 192.168.1.73:3000 weight=1;
}

log_format main '$remote_addr $remote_user [$time_local] "$request" '
                '$status $body_bytes_sent "$http_referer" '
                '"$http_user_agent" to: "$upstream_addr" upstream_status: "$upstream_status" '
                'host: $host:$server_port time: $request_time $upstream_response_time';

server {
  listen 80;
  server_name grafana.local;

  access_log /var/log/nginx/grafana-access.log main;
  error_log /var/log/nginx/grafana-error.log;

  location / {
    proxy_pass http://grafana-backend;
    proxy_set_header Host $host;
    proxy_set_header X-Real-IP $remote_addr;
    proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
    proxy_set_header X-Forwarded-Proto $scheme;
  }
}
```



удаляем старый конфиг
```
sudo rm /etc/nginx/sites-available/default
```

создаем ссылку
 
```
sudo ln -s /etc/nginx/sites-available/grafana.conf /etc/nginx/sites-enabled/grafana.conf
```



Traefik конфиг через cloundflare

```
http:
  routers:
    grafana-router:
      entryPoints:
        - https  # Используем HTTPS
      rule: "Host(`dns.a87p.ru`)"  # Правило маршрутизации
      service: grafana-service  # Привязка к сервису
      tls:
        certResolver: cloudflare  # Используем Cloudflare для автоматической выдачи сертификатов

  services:
    grafana-service:
      loadBalancer:
        servers:
          - url: "http://192.168.1.81:3000"  # Первый сервер Grafana
          - url: "http://192.168.1.73:3000"  # Второй сервер Grafana
        passHostHeader: true  # Передача исходного заголовка Host
        healthCheck:
          path: "/api/health"  # Путь для проверки здоровья (укажите правильный путь для вашего сервера Grafana)
          interval: "10s"  # Интервал между проверками
          timeout: "5s"  # Таймаут для проверки

  middlewares:
    headers-middleware:
      headers:
        customRequestHeaders:
          X-Real-IP: "{remote_ip}"
          X-Forwarded-For: "{remote_ip}"
          X-Forwarded-Proto: "https"  # Протокол https
          Host: "{host}"
```