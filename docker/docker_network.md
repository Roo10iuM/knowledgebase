# Docker: Network

Для работы с сетями в Docker используются драйверы. Имеется несколько драйверов по умолчанию, которые обеспечивают основной функционал по работе с сетью:

- none: отключение всех сетевых ресурсов
- bridge: сетевой драйвер по умолчанию. Это виртуальный сетевой мост, соединяющий контейнеры между собой и с хост-системой. Мостовые сети обычно используются, когда приложения выполняются в автономных контейнерах, которые должны взаимодействовать друг с другом
- host: для автономных контейнеров устраняется сетевая изолированность между контейнером и хостом Docker и напрямую используются сетевые ресурсы хоста
- overlay: наложенные сети соединяют несколько демонов Docker
- macvlan: сети Macvlan позволяют присваивать контейнеру MAC-адрес, благодаря чему он выглядит как физическое устройство в сети

## Команды для работы с сетевыми интерфейсами

Чтобы посмотреть доступные сети Docker можно воспользоваться командой `docker network ls`:

```
# docker network ls
NETWORK ID     NAME      DRIVER    SCOPE
034b18b39364   bridge    bridge    local
7993e1192e62   host      host      local
359f8ff1c239   none      null      local
```

Новый сетевой интерфейс можно создать командой `docker network create`:

```
# docker network create -d bridge mynet
```

Опция `-d` указывает docker драйвер для управления сетью.

Создадим следующий Dockerfile:

```
FROM python:3-slim
WORKDIR /app
RUN echo "hello world" > index.html
ENTRYPOINT ["python", "-m", "http.server", "8000"]
```

Запустим его, опция `--network` указывает сеть для контейнера:

```
# docker build -t httpserv -f dnet .
# docker run --rm --network=mynet --name server httpserv
```

Создадим контейнер alpine и попробуем подключиться к HTTP-серверу:

```
# docker run --rm -it --name client alpine
# apk add --update curl
```

С помощью команды `docker inspect mynet` можно узнать IP-адреса контейнеров использующих драйвер mynet, в моём случае это `172.18.0.2`. Попробуем подключиться к серверу с помощью `curl`:

```
# curl http://172.18.0.2:8000
curl: (28) Failed to connect to 172.18.0.2 port 8000 after 135898 ms: Could not connect to server
```

Так при запуске контейнера не была использована опция `--network` получить доступ к серверу не вышло. В таком сценарии можно использовать команду: 

`# docker network connect mynet client`

Попробуем подключиться к серверу ещё раз:

```
# curl http://172.18.0.2:8000
hello world
```

Остановим контейнеры. Удалим сетевой драйвер командой:

`# docker network rm mynet`

## Sources

- [Docker doc](https://docs.docker.com/engine/network/)
- [habr Docker и сети](https://habr.com/ru/companies/otus/articles/730798/)
- [dockerlabs](https://github.com/collabnix/dockerlabs)