# Docker: Dockerfile

## Introduction

Dockerfile - это файл, содержащий инструкции для создания образа Docker. Контейнеры состоят из слоёв. Каждый слой, кроме последнего, находящегося поверх всех остальных, предназначен только для чтения. Dockerfile сообщает системе Docker о том, какие слои и в каком порядке надо добавить в образ. 

Рассмотрим базовые инструкции.

## FROM

Файл Dockerfile должен начинаться с инструкции FROM, или с инструкции ARG, за которой идёт инструкция FROM. 

[FROM](https://docs.docker.com/reference/dockerfile/#from) сообщает Docker о том, чтобы при сборке образа использовался бы базовый образ, который соответствует предоставленному имени и тегу. Базовый образ, кроме того, ещё называют родительским образом.

## RUN

[RUN](https://docs.docker.com/reference/dockerfile/#run) позволяет создать слой во время сборки образа. После её выполнения в образ добавляется новый слой, его состояние фиксируется. Инструкция RUN часто используется для установки в образы дополнительных пакетов.

## Простой Dockerfile

Напишем первый Dockerfile:

```
# Сообщаем базовый образ
FROM alpine:3.22
# Обновляем пакеты
RUN apk update && apk upgrade
# Устанавливаем git
RUN apk add git
```

Соберём образ с помощью команды:

`# docker build -t DOCKHUB/alpine-git:1.0 .`

Проверим образ:

```
# docker run --rm DOCKHUB/alpine-git:1.0 git --version
git version 2.49.0
```

## COPY и ADD

[COPY](https://docs.docker.com/reference/dockerfile/#copy) сообщает Docker о том, что нужно взять указанные файлы и папки и добавить их в указанную целевую директорию образа. Если целевая директория не существует, эта инструкция её создаст.

[ADD](https://docs.docker.com/reference/dockerfile/#add) позволяет решать те же задачи, что и COPY, но с помощью этой инструкции можно добавлять в контейнер файлы, загруженные из удалённых источников.

```
FROM alpine:3.22
ADD URL /usr/src/things/
COPY file1.txt file2.txt /usr/src/things/
```

## CMD и ENTRYPOINT

[CMD](https://docs.docker.com/reference/dockerfile/#cmd) предоставляет Docker команду, которую нужно выполнить при запуске контейнера. Результаты выполнения этой команды не добавляются в образ во время его сборки.

[ENTRYPOINT](https://docs.docker.com/reference/dockerfile/#entrypoint) позволяет задавать команду с аргументами, которая должна выполняться при запуске контейнера. Она похожа на команду CMD, но параметры, задаваемые в ENTRYPOINT, не перезаписываются в том случае, если контейнер запускают с параметрами командной строки.

Dockerfile:

```
FROM alpine:3.22
ENTRYPOINT ["ls"]
CMD ["-l"]
```

Запуск 1 (res: `ls -l`):

```
# docker run lstest
total 56
drwxr-xr-x    2 root     root          4096 May 30 12:13 bin
drwxr-xr-x    5 root     root           340 Jul  6 19:05 dev
drwxr-xr-x    1 root     root          4096 Jul  6 19:05 etc
```

Запуск 2 (res: `ls -a`):

```
# docker run lstest -a
.
..
.dockerenv
bin
dev
etc
```

## ARG и ENV

[ARG](https://docs.docker.com/reference/dockerfile/#arg) позволяет задать переменную, значение которой можно передать из командной строки в образ во время его сборки. 


[ENV](https://docs.docker.com/reference/dockerfile/#env) позволяет задавать постоянные переменные среды, которые будут доступны в контейнере во время его выполнения.

Dockerfile:

```
FROM alpine:3.22
ARG CONT_IMG_VER
ENV CONT_IMG_VER=${CONT_IMG_VER:-v1.0.0}
CMD [ "sh", "-c", "echo $CONT_IMG_VER"]
```

```
# docker build -t argenv .
# docker run --rm argenv
v1.0.0
# docker build --build-arg CONT_IMG_VER=v2.0.0 -t argenv .
# docker run --rm argenv
v2.0.0
```

## ONBUILD

[ONBUILD](https://docs.docker.com/reference/dockerfile/#onbuild) добавляет к образу инструкцию триггера, которая будет выполнена в более позднее время, когда образ используется в качестве основания для другой сборки.

Создадим такую структуру каталогов.

```
$ tree f1
f1
├── f1.txt
└── f2
    └── f2.txt
```

Создадим Dockerfile: 

```
FROM alpine:3.22
ONBUILD COPY . /start_dir
```

Соберём образ `myon`:

`# docker build -t myon .`

Создадим Dockerfile и используем myon как базовый:

```
FROM myon
CMD ["ls", "/start_dir"]
```

Соберём образ разными способами и запустим контейнеры:

Пример 1 (контекст: директория f1):

```
# docker build -t ontest f1/f2
# docker run --rm ontest
f1.txt
f2
```

Пример 2 (контекст: директория f1/f2):

```
# docker build -t ontest f1/f2
# docker run --rm ontest
f2.txt
```

## LABEL, EXPOSE, SHELL, WORKDIR, VOLUME

[LABEL](https://docs.docker.com/engine/reference/builder/#label) (метка) позволяет добавлять в образ метаданные. 

[EXPOSE](https://docs.docker.com/reference/dockerfile/#expose) указывает на то, какие порты планируется открыть для того, чтобы через них можно было бы связаться с работающим контейнером.

[SHELL](https://docs.docker.com/reference/dockerfile/#shell) используется для определения используемой оболочки (shell) для выполнения команд, таких как RUN, CMD и ENTRYPOINT.

[WORKDIR](https://docs.docker.com/reference/dockerfile/#workdir) позволяет изменить рабочую директорию контейнера. С этой директорией работают инструкции COPY, ADD, RUN, CMD и ENTRYPOINT, идущие за WORKDIR.

[VOLUME](https://docs.docker.com/reference/dockerfile/#volume) указывает точку монтирования, которую контейнер будет использовать для постоянного хранения файлов и для работы с такими файлами.

## Sources

- [Изучаем Docker](https://habr.com/ru/companies/ruvds/articles/439980/)
- [Docker doc](https://docs.docker.com/reference/dockerfile/)
- [habr multi-stage builds](https://habr.com/ru/articles/349802/)
- [dockerlabs](https://github.com/collabnix/dockerlabs)