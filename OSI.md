# OSI

## Introduction

Модель OSI (Open Systems Interconnection) - это модель, разработанная для описания функций телекоммуникационных или вычислительных систем, необходимых для сетевого взаимодействия. Она разделяет процесс сетевого взаимодействия на семь взаимосвязанных уровней:

- Физический уровень (Physical)
- Канальный уровень (Data Link)
- Сетевой уровень (Network)
- Транспортный уровень (Transport)
- Сеансовый уровень (Session)
- Уровень представления (Presentation)
- Уровень приложений или прикладной уровень (Application)

Каждый уровень выполняет специфические функции и взаимодействует с уровнями непосредственно выше и ниже. На седьмом уровне информация представляется в виде данных, на первом — в виде бит: 
```
    биты → кадры → пакеты → сегменты → данные приложения
```
Процесс, когда информация отправляется и переходит из данных в биты, называется инкапсуляцией. Обратный процесс, когда информация, полученная в битах на первом уровне, переходит в данные на седьмом, называется декапсуляцией.



Все семь уровней модели OSI можно условно разделить на две группы:

- Media layers (уровни среды),
- Host layers (уровни хоста).

Уровни группы Media Layers (L1, L2, L3) занимаются передачей информации (по кабелю или беспроводной сети), используются сетевыми устройствами, такими как коммутаторы, маршрутизаторы и т.п. Уровни группы Host Layers (L4, L5, L6, L7) используются непосредственно на устройствах, будь то стационарные компьютеры или мобильные устройства.

## Физический уровень (Physical)

Физический уровень  предназначен для передачи данных в форме электрических, световых или радиосигналов. Его функции ограничиваются исключительно передачей и приемом сигналов, без учета их логики или содержания данных. На физическом уровне оперируют битами.

Примеры протоколов/стандартов уровня:

- IEEE 802.11 (Wi-Fi)
- IEEE 802.15 (Bluetooth)
- IEEE 802.3 (Ethernet)

<details>
<summary>Что такое IEEE?</summary><br>
Институт инженеров электротехники и электроники — IEEE (англ. Institute of Electrical and Electronics Engineers).
</details>

## Канальный уровень (Data Link)

У канального уровня есть два подуровня — это MAC и LLC. MAC (Media Access Control, контроль доступа к среде) отвечает за присвоение физических MAC-адресов, а LLC (Logical Link Control, контроль логической связи) занимается проверкой и исправлением данных, управляет их передачей. На канальном уровне оперируют кадрами (frames).

Примеры протоколов/стандартов уровня:

- IEEE 802.3 (Ethernet)
- ARP (Address Resolution Protocol — протокол определения адреса)
- Point-to-Point Protocol (PPP)

## Сетевой уровень (Network)

Сетевой уровень модели предназначен для определения пути передачи данных. Отвечает за трансляцию логических адресов и имён в физические, определение кратчайших маршрутов, коммутацию и маршрутизацию, отслеживание неполадок и «заторов» в сети. На сетевом уровне оперируют пакетами. 

Примеры протоколов/стандартов уровня:

- IP/IPv4/IPv6 (Internet Protocol)
- OSPF (Open Shortest Path First) — протокол динамической маршрутизации, использующий для нахождения кратчайшего пути алгоритм Дейкстры.

## Транспортный уровень (Transport)

Транспортный уровень отвечает за надежную передачу данных, поддерживая их целостность и последовательность между устройствами. На транспортном уровне оперируют сегментами и датаграммами. 

Примеры протоколов/стандартов уровня:

- TCP (Transmission Control Protocol)
- UDP (User Datagram Protocol)

## Сеансовый уровень (Session)

Сеансовый уровень управляет сеансами связи между приложениями. Обеспечивает установку, поддержание и завершение сеансов, а также синхронизацию и управление обменом данными.

Примеры протоколов/стандартов уровня:

- PPTP (Point-to-Point Tunneling Protocol)
- H.245 — протокол согласования параметров соединения. Описывает процедуру установления общего набора мультимедийных возможностей. В рамках процедуры установления вызова между оборудованием (программами) стороны обмениваются информацией о кодеках и договариваются о том, на каком из них будет проходить разговор.

## Уровень представления (Presentation)

Уровень представления обеспечивает кодирование/декодирование данных. Запросы приложений, полученные с прикладного уровня, на уровне представления преобразуются в формат для передачи по сети, а полученные из сети данные преобразуются в формат приложений. На этом уровне может осуществляться сжатие/распаковка или шифрование/дешифрование. 

Примеры протоколов/стандартов уровня:

- SSL (Secure Sockets Layer)
- TLS (Transport Layer Security)

## Уровень приложений или прикладной уровень (Application)

Прикладной уровень предоставляет пользователям возможность взаимодействия с сетью через приложения и сервисы.

Примеры протоколов/стандартов уровня:

- HTTP (HyperText Transfer Protocol)
- DNS (Domain Name System)
- DHCP (Dynamic Host Configuration Protocol)
- FTP (File Transfer Protocol)

## Схема инкапсуляции данных (HTTP → Ethernet)

Уровень                | Данные                        | Протоколы
-----------------------|------------------------------ |----------------------
**Прикладной (L7)**    | `GET /index.html HTTP/1.1`    | HTTP/HTTPS
**Представления (L6)** | `[Зашифрованные данные]`      | TLS
**Сеансовый (L5)**     | `[Заголовок сеанса][Данные]`  | PPTP
**Транспортный (L4)**  | `[TCP-заголовок][Данные]`     | TCP
**Сетевой (L3)**       | `[IP-заголовок][TCP-сегмент]` | IPv4
**Канальный (L2)**     | `[MAC][LLC][IP-пакет][FCS]`   | Ethernet + 802.2 LLC
**Физический (L1)**    | `1011100100011100...`         | IEEE 802.3

## Sources

- [wikipedia](https://ru.wikipedia.org/wiki/%D0%A1%D0%B5%D1%82%D0%B5%D0%B2%D0%B0%D1%8F_%D0%BC%D0%BE%D0%B4%D0%B5%D0%BB%D1%8C_OSI)
- [selectel](https://selectel.ru/blog/osi-for-beginners/)
