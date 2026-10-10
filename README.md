#1
Вывести отсортированный в алфавитном порядке список имен пользователей в файле passwd (вам понадобится grep).
Решение:
labex:project/ $ grep -o '^[^:]*' /etc/passwd | sort
Ответ:
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

## Задача 2

Вывести данные /etc/protocols в отформатированном и отсортированном порядке для 5 наибольших портов, как показано в примере ниже:

```
[root@localhost etc]# cat /etc/protocols ...
142 rohc
141 wesp
140 shim6
139 hip
138 manet
```
Решение:
labex:project/ $ grep -v '^#' /etc/protocols | awk 'NF > 0 {print $2, $1}' | sort -nr | head -5 
Ответ:
142 rohc
141 wesp
140 shim6
139 hip
138 manet

## Задача 3

Написать программу banner средствами bash для вывода текстов, как в следующем примере (размер баннера должен меняться!):

```
[root@localhost ~]# ./banner "Hello from RTU MIREA!"
+-----------------------+
| Hello from RTU MIREA! |
+-----------------------+
```
Решение:
labex:project/ $ nano banner
labex:project/ $ chmod +x banner
labex:project/ $ ./banner Hello\ from\ RTU\ MIREA\! 
+-----------------------+
| Hello from RTU MIREA! |
+-----------------------+


