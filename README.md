# Лабораторная работа: Работа с командной строкой Linux

## Задача 1

**Условие:** Вывести отсортированный в алфавитном порядке список имен пользователей в файле `passwd`.

**Решение:**
```bash
grep -o '^[^:]*' /etc/passwd | sort
```

**Результат:**
```
_apt
avahi
backup
bin
colord
daemon
games
gnats
irc
labex
list
lp
mail
man
messagebus
mongodb
mysql
news
nobody
proxy
pulse
redis
root
rtkit
saned
sshd
sync
sys
systemd-network
systemd-resolve
systemd-timesync
tcpdump
usbmux
uucp
www-data
```

---

## Задача 2

**Условие:** Вывести данные `/etc/protocols` в отформатированном и отсортированном порядке для 5 наибольших портов.

**Пример вывода:**
```
142 rohc
141 wesp
140 shim6
139 hip
138 manet
```

**Решение:**
```bash
grep -v '^#' /etc/protocols | awk 'NF > 0 {print $2, $1}' | sort -nr | head -5
```

**Результат:**
```
142 rohc
141 wesp
140 shim6
139 hip
138 manet
```

---

## Задача 3

**Условие:** Написать программу `banner` средствами bash для вывода текстов с изменяемым размером баннера.

**Пример работы:**
```
[root@localhost ~]# ./banner "Hello from RTU MIREA!"
+-----------------------+
| Hello from RTU MIREA! |
+-----------------------+
```

**Решение:**

Создание скрипта:
```bash
nano banner
chmod +x banner
```

Содержимое `banner`:
```bash
#!/bin/bash

text="$*"
len=${#text}
line=$(printf '+%*s+' "$((len + 2))" "" | tr ' ' '-')

echo "$line"
echo "| $text |"
echo "$line"
```

**Запуск:**
```bash
./banner Hello from RTU MIREA!
```

**Результат:**
```
+-----------------------+
| Hello from RTU MIREA! |
+-----------------------+
```
