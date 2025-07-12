# Docker: Compose

Docker Compose - это инструмент для определения и запуска мульти-контейнерных приложений. 

Compose упрощает управление стеком приложений, позволяя легко управлять сервисами, сетями и томами (volumes) в одном файле конфигурации YAML. Затем, одной командой, вы создаете и запускаете все сервисы из файла конфигурации.

## Установка

`# pacman -S docker-compose`

Проверка установки:

```
$ docker compose -v
Docker Compose version 2.38.2
```

## [Как работает Compose](https://docs.docker.com/compose/intro/compose-application-model/)

По умолчанию compose файл называется `compose.yaml` и находится в рабочей директории. Docker CLI позволяет взаимодействовать с Docker Compose через команду `docker compose`. Основными командами являются:

- `docker compose up` запускает сервисы прописанные в `compose.yaml`
- `docker compose down` останавливает и удаляет сервисы
- `docker compose logs` позволяет посмотреть логи
- `docker compose ps` выводит список сервисов и их статус

## Примеры compose файлов

Рассмотрим некоторые docker compose файлы, которые встречаются при изучении docker compose через [docs.dockerdocs.com](https://docs.docker.com/).

Первый compose файл встречается в статье [How Compose works](https://docs.docker.com/compose/intro/compose-application-model/):

```
# определение сервисов приложения
services:
  # сервис фронтенда 
  frontend:
    # задаём образ
    image: example/webapp
    # пробрасываем порт 443 хоста, на порт 8043 контейнера
    ports:
      - "443:8043"
    # подключаем сервис к двум сетям
    networks:
      - front-tier
      - back-tier
    # подключаем конфигурационные данные
    configs:
      - httpd-config
    # подключаем секрет
    secrets:
      - server-certificate
  # сервис бэкенд 
  backend:
    # задаём образ
    image: example/database
    # монтируем том db-data в /etc/data
    volumes:
      - db-data:/etc/data
    # подключаем сервис к сети
    networks:
      - back-tier

# объявляем том db-data с драйвером flocker
# в качестве опции для драйвера передаём желаемый размер
volumes:
  db-data:
    driver: flocker
    driver_opts:
      size: "10GiB"

# указываем, что конфигурационные данные берутся извне 
configs:
  httpd-config:
    external: true

# секрет может быть файлом или переменной окружения
# в данном случае мы сообщаем, что он является внешним
# и должен быть взят из Docker Swarm или другого места
# позволяющего управлять секретами
# по умолчанию, если секрет - это файл, 
# то он монтируется в /run/secrets/<secret_name>
secrets:
  server-certificate:
    external: true

# пример создания конфига и секрета
# docker config create httpd-config ./config.conf
# docker secret create server-certificate ./cert.pem

# Определяем сети
networks:
  front-tier: {}
  back-tier: {}
```

Далее рассмотрим compose файл из статьи [Docker Compose Quickstart](https://docs.docker.com/compose/gettingstarted/) разбитый на две части `infra.yaml`:

```
# Создаём сервис redis
services:
  redis:
    image: "redis:alpine"
```

И `compose.yaml`:

```
# Включаем infra.yaml в конфигурацию
include:
   - infra.yaml
services:
  web:
    # используем образ основанный на Dockerfile в текущем каталоге
    build: .
    # пробрасываем порты
    ports:
      - "8000:5000"
    # используем watch для упрощения разработки
    # данный атрибут позволит синхронизировать изменения
    # в текущем каталоге с каталогом /code
    develop:
      watch:
        - action: sync
          path: .
          target: /code
```

## Sources

- [Compose file reference](https://docs.docker.com/reference/compose-file/)
- [How Compose works](https://docs.docker.com/compose/intro/compose-application-model/)
- [habr Docker Compose для начинающих](https://habr.com/ru/companies/ruvds/articles/450312/)
- [dockerlabs](https://github.com/collabnix/dockerlabs)