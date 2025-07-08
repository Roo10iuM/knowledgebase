# Docker: базовые команды

## Introduction

Docker - это платформа с открытым исходным кодом для автоматизации развертывания, доставки и запуска приложений в контейнерах. 

Контейнер — это изолированное пространство, которое позволяет запускать приложения с их зависимостями отдельно от основной системы.

Основой для создания контейнеров Docker являются образы (images). Образ Docker (Docker image) - это неизменяемый, только для чтения шаблон, который содержит все необходимое для запуска приложения: код, библиотеки, переменные окружения, файлы конфигурации и другие зависимости.

## Установка 

`# pacman -S docker`

Запустим docker.service:

`# systemctl start docker.service`

Проверим работоспособность:

`# docker info`

> Если требуется использовать команду docker от имени обычного пользователя, добавьте его в группу docker, перелогиньтесь и перезапустите службу docker.service.
>
> **Важно**: Каждый пользователь в группе docker имеет права, равноценные правам суперпользователя, поскольку они могут использовать docker run --privileged для запуска контейнеров с привилегиями суперпользователя. ([Arch wiki](https://wiki.archlinux.org/title/Docker_(%D0%A0%D1%83%D1%81%D1%81%D0%BA%D0%B8%D0%B9)#%D0%A3%D1%81%D1%82%D0%B0%D0%BD%D0%BE%D0%B2%D0%BA%D0%B0))

## Начало работы

Воспользуемся командой `docker run` и запустим первый контейнер:

`# docker run hello-world`

### Базовые команды:
- `docker run` запускает контейнер, опция `--rm` позволяет автоматически удалять контейнер после завершения
- `docker images` выводит список установленных образов.
- `docker pull NAME[:TAG|@DIGEST]` загрузит образ NAME из репозитория
- `docker ps` выводит список работающих контейнеров, опция `-a` позволяет вывести все контейнеры
- `docker stop` штатное завершение работы контейнера
- `docker kill` принудительное завершение работы контейнера
- `docker restart` перезапускает контейнер, выполняя остановку и затем запуск
- `docker rm` удаляет контейнеры
- `docker rm $(docker ps -aq -f status=exited)` удаляет все остановленные контейнеры
- `docker rmi` удаляет образы

## Alpine и Docker Hub

Alpine Linux - это легковесный, безопасный и ресурсоэффективный дистрибутив Linux, часто используемый для контейнеров Docker.

Следующая команда позволит загрузить образ Alpine с Docker Hub:

`# docker pull alpine`

Docker Hub — это общедоступный реестр образов контейнеров.

Запустим контейнер в интерактивном режиме:

`# docker run -it alpine`

Чтобы выйти и оставить контейнер запущенным, можно воспользоваться комбинацией клавиш `Ctrl+P Ctrl+Q`. Чтобы снова вернуться к контейнеру, можно воспользоваться командой `docker attach`. Для выполнения единичной команды внутри запущенного контейнера можно воспользоваться `docker exec`.

Выполним команду `apk update` и установим git командой `apk add git`. Зафиксируем изменения командой:

`# docker commit -m "added git" CONTAINER DOCKHUB_NAME/alpine-git`

```
# docker images
REPOSITORY                TAG       IMAGE ID       CREATED         SIZE
DOCKHUB_NAME/alpine-git   latest    f775623f8ecc   5 minutes ago   23.4MB
```

Создадим тег для получившегося образа:

`# docker tag DOCKHUB_NAME/alpine-git:latest DOCKHUB_NAME/alpine-git:1.0`

```
# docker images
REPOSITORY                TAG       IMAGE ID       CREATED         SIZE
DOCKHUB_NAME/alpine-git   1.0       f775623f8ecc   5 minutes ago   23.4MB
DOCKHUB_NAME/alpine-git   latest    f775623f8ecc   5 minutes ago   23.4MB
```

Войдём в реестр и отправим полученный образ:

```
# docker login
# docker push DOCKHUB_NAME/alpine-git:1.0
```

## Sources

- [Arch wiki](https://wiki.archlinux.org/title/Docker_(%D0%A0%D1%83%D1%81%D1%81%D0%BA%D0%B8%D0%B9))
- [dockerlabs](https://github.com/collabnix/dockerlabs)