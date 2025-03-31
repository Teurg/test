# Docker Update Role

Роль для обновления Docker-проектов с использованием `docker-compose`. Эта роль обновляет образы для каждого проекта, перезапускает сервисы и удаляет неиспользуемые образы.

## Переменные

- `docker_projects`: Список Docker-проектов, для которых нужно обновить образы.

Пример:
```yaml
docker_projects:
  - diun
  - gotify
  - guacamole
  - homarr
  - jellyfin
  - portainer
  - traefik
  - uptime-kuma
  - vaultwarden
  - wireguard
