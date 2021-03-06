# Решения заданий лабораторной работы №6 (Компьютерные сети)

## Зона DMZ. Назначение и типы архитектур.
Зона DMZ(*Demilitarized Zone*) - это часть сети, которая содержит общедоступные сервисы(*например почтовый сервис*) и отделяет их от остальной сети.

DMZ позволяет:
 - разделить контроль над общими ресурсами и приватными(*появляется возможность устанавливать правила доступа к ресурсам более гибко*).
 - повысить безопасность сети(*если атака на DMZ пройдет успешно, то приватная часть сети останется нетронутой*).

Наиболее распространенными типами архитектур являются: DMZ с одним межсетевым экраном и с двумя.
 - Архитектура с одним экраном связывает все части сети(*приватную сеть, общие ресурсы и WAN*) одним устройством.
 Это устройство является слабым местом - единая точка отказа.
 - Архитектура с двумя экранами предоставляет отдельный экран для приватной сети и для DMZ.
 В таком случае безопасность сети становится выше, так как отсутствует единая точка отказа.

## Firewall. Назначение, принцип работы.
Firewall(*межсетевой экран*) - это инструмен(*часть сети*), который позволяет фильтровать, проходящий через него трафик, согласно установленным правилам.

Фильтрация трафика может быть реализована согласно двум принципам:
 - "Что не запрещено явно, то разрешено" - если пакет не попадает ни под один из запретов, то он проходит фильтр.
 - "Что не разрешено явно, то запрещено" - если пакет не входит в список разрешенных, то он не проходит фильтр.

## Windows Firewall.
### Продемонстрировать 3 - 4 примера: запрет на приложение, протокол, порт.
1. Запретить весь исходящий трафик для приложения Internet Explorer
![Deny all for IE](https://github.com/mihai-valentin/computer_networks_acl/blob/main/deny_all.jpg)
2. Запретить исходящий трафик для приложения Internet Explorer по протоколу TCP
![Deny tcp for IE](https://github.com/mihai-valentin/computer_networks_acl/blob/main/deny_tcp.jpg)
3. Запретить исходящий трафик для приложения Internet Explorer по протоколу TCP порт 80
![Deny tcp::80 for IE](https://github.com/mihai-valentin/computer_networks_acl/blob/main/deny_tcp_80.jpg)
4. Разрешить исходящий трафик для приложения Internet Explorer по протоколу TCP
![Permit tcp for IE](https://github.com/mihai-valentin/computer_networks_acl/blob/main/permit_tcp.jpg)

## Linux Firewall.
### Привести 2 - 3 примера по настройки правил.

Для настройки межсетевого экрана в linux можно использовать утилиту iptables. Установка: `sudo apt-get install iptables` .
1. Разрешить/Запретить весь входящий трафик
```
iptables --policy INPUT ACCEPT
iptables --policy INPUT DROP
```
2. Запретить все соединения с google.com(216.58.198.14) `iptables -A INPUT -s 216.58.198.14 -j DROP`
3. Запретить все соединения для нескольких адресов `iptables -A INPUT -s 192.168.6.0/24 -j DROP`
4. Запретить все соединения с google.com(216.58.198.14) по протоколу tcp `iptables -A INPUT -p tcp -s 216.58.198.14 -j DROP`
5. Запретить все соединения с google.com(216.58.198.14) по протоколу tcp на порт 80 `iptables -A INPUT -p tcp --dport 80 -s 216.58.198.14 -j DROP`

## Списки ACL. Назначение, классификация, правила применения.
### Привести примеры создания стандартных и расширенных ACL.

ACL(*access control list*) - это список правил фильтрации для входящего и/или исходящего трафика в сети.

ACL классифицируются по типу:
 - Стандартный <1 - 99>: разрешает или блокирует доступ для источника. 
 - Расширенный <100 - 199>: разрешает или блокирует трафик для источника и назначения с возможностью указать протокол и порт

ACL классифицируются по направлению:
 - Inside - трафик идущий в сторону роутера
 - Outside - трафик из роутера к сети назначения

Стандартные ACL следует применять как можно ближе к сети назначения трафика.
Например, если запрос идет из сети A в сеть B, тогда стандартный ACL следует применить на интерфейсе, который расположен ближе всех к сети B.

Расширенные ACL следует применять как можно ближе к источнику, то есть на интерфейсе, который расположен непосредственно перед источником.

Создание стандартного ACL:
 - Разрешить доступ для хоста 192.168.0.1 `access-list 1 permit 192.168.0.1 0.0.0.0`
 - Запретить доступ для хоста 192.168.0.2 `access-list 1 deny 192.168.0.2 0.0.0.0`
 - Применить ACL на интерфейс `ip access-group 1 outside`

Создание расширенного ACL:
 - Разрешить доступ для хоста 192.168.0.1 по всем протоколам во все направления `access-list 100 permit ip 192.168.0.1 0.0.0.0 any`
 - Применить ACL на интерфейс `ip access-group 100 inside`

## Выполнение операций в Packet Tracer.
### Добавление/Удаление списка. Изменение списка в определенной строке: добавить, удалить, изменить правило.

Операции над ACL:
 - Добавить(создать список или добавить правило в конец) `access-list <номер> <опции>`
 - Удалить `no access-list <номер>`
 - Добавить правило в существующий список
   - Войти в режим редактирования `ip access-list <тип> <номер|название>`
   - Добавить правило `<позиция> <permit|deny> <опции>`
 - Удалить правило из существующего списка
   - Войти в режим редактирования `ip access-list <тип> <номер|название>`
   - Добавить правило `no <позиция>`
 - Изменить правило в существующем списке
   - Удалить правило по номеру(*описано выше*)
   - Добавить правило с тем же номером(*описано выше*)
   
## Настроить схему в Packet Tracer
Демонстрация конечного результата начинается на 40 минуте.
[Видео с полной настройкой сети(44 мин)](https://drive.google.com/file/d/19WffoJo5xk8aBrWFnrkA1yseiE-U3BPv/view?usp=sharing)
