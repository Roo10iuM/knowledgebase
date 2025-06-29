# QEMU

QEMU (Quick EMUlator) - это свободная программа с открытым исходным кодом, которая может использоваться как эмулятор аппаратного обеспечения, так и для виртуализации.

В качестве примера работы с qemu, создадим виртуальную машину. Гостевой системой будет debian.

## Debian

Для начала загрузим dvd образ ОС с [официального сайта](https://www.debian.org/).

## Установим QEMU

```
# Ubuntu/Debian
sudo apt install qemu-system-x86

# Fedora/RHEL
sudo dnf install qemu-kvm qemu-img

# Arch Linux
sudo pacman -S qemu-full
```

## Создадим образ

Чтобы создать образ воспользуемся командой `qemu-img`.

`$ qemu-img create -f qcow2 debOS.qcow2 20G`

- `-f qcow2` - формат образа
- `debOS.qcow2` - название образа
- `20G` - размер образа
<details>
<summary>О qcow2</summary><br>
qcow2 - формат, который выделяет место под файл образа только тогда, когда гостевая операционная система фактически записывает данные в эти секторы своего виртуального жесткого диска. Гостевой ОС образ видится полного размера, хотя реально на хост-системе он может занимать очень небольшой объем пространства. Этот формат образа также поддерживает функциональность создания снимков состояния (snapshots) QEMU. Однако использование этого формата вместо raw может негативно сказаться на производительности.
</details><br>


## Устанавливаем систему

Напишем bash скрипт для удобства. Не забываем сделать его исполняемым с помощью `chmod`.

```
#!/bin/bash
qemu-system-x86_64 -hda debOS.qcow2 \
                   -enable-kvm \
                   -cpu host \
                   -m 4096 \
                   -device virtio-vga-gl \
                   -display sdl,gl=on \
                   -cdrom debian-12.10.0-amd64-DVD-1.iso \
                   -boot order=d
```

- `qemu-system-x86_64` - выбираем архитектуру x86_64
- `-hda debOS.qcow2` - подключаем виртуальный жёсткий диск
- `-enable-kvm` - включаем [kvm](https://habr.com/ru/articles/466549/)
- `-cpu host` - используем cpu системы
- `-m 4096` - выделяем 4096 МБ ОЗУ
- `-device virtio-vga-gl` - добавляем VGA
- `-display sdl,gl=on` - включаем OpenGL для дисплея
- `-cdrom debian-12.10.0-amd64-DVD-1.iso` - подключаем cdrom
- `-boot order=d` - изменяем порядок загрузки, чтобы загрузиться с cdrom

## Bash скрипт для запуска VM

После установки ОС можно удалить или закомментировать лишние строки.

```
#!/bin/bash
qemu-system-x86_64 -hda debOS.qcow2 \
                   -enable-kvm \
                   -cpu host \
                   -m 4096 \
                   -device virtio-vga-gl \
                   -display sdl,gl=on
```

## Sources

- [Debian mirrors](https://www.debian.org/CD/http-ftp/)
- [QEMU Arch wiki](https://wiki.archlinux.org/title/QEMU)
    - [Installation](https://wiki.archlinux.org/title/QEMU#Installation)
    - [Creating_a_new_virtualized_system](https://wiki.archlinux.org/title/QEMU#Creating_a_new_virtualized_system)
    - [Running_a_virtualized_system](https://wiki.archlinux.org/title/QEMU#Running_a_virtualized_system)
    - [virtio](https://wiki.archlinux.org/title/QEMU#virtio)
- [habr быстрый старт](https://habr.com/ru/sandbox/244526/)
- [habr общие принципы работы QEMU-KVM](https://habr.com/ru/articles/466549/)