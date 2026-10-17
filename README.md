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
## Задача 4
**Условие:** Написать программу для вывода всех идентификаторов (по правилам C/C++ или Java) в файле (без повторений).
**Пример работы для hello.c:**

```
h hello include int main n printf return stdio void world
```
**Решение:**

Создание скрипта:
```bash
nano 4task
```
Создание файла:
```bash
nano hello.c
```
``` C
#include <stdio.h>

int main(void) {
    printf("Hello, world!\n");
    return 0;
}
```
Содержимое :
```bash
!/bin/bash
grep -oE '[a-zA-Z_][a-zA-Z0-9_]*' "$1" | sort -u | tr '\n' ' '
echo
```

**Запуск:**
```bash
chmod +x 4task
./4task hello.c
```

**Результат:**
```
h Hello include int main n printf return stdio void world
```
## Задача 5
**Условие:** Написать программу для регистрации пользовательской команды (правильные права доступа и копирование в /usr/local/bin).

Например, пусть программа называется reg:
```
./reg banner
```
**Решение:**
Создание скрипта:
```bash
nano reg
``` 
``` bash
#!/bin/bash
# Регистрация пользовательской команды в /usr/local/bin

if [ $# -ne 1 ]; then
    echo "Использование: $0 <файл_команды>" >&2
    exit 1
fi

file="$1"

if [ ! -f "$file" ]; then
    echo "Ошибка: файл '$file' не найден" >&2
    exit 1
fi

chmod 755 "$file"
sudo cp "$file" /usr/local/bin/
echo "Команда '$(basename "$file")' зарегистрирована в /usr/local/bin"
```
**Запуск:**
chmod +x reg
./reg banner
**Результат:**
```
Команда 'banner' зарегистрирована в /usr/local/bin
```
## Задача 6
**Условие:** Написать программу для проверки наличия комментария в первой строке файлов с расширением c, js и py.
**Решение:**
Создание скрипта:
nano check_comment.sh
Содержимое:
#!/bin/bash
dir="${1:-.}"

find "$dir" -type f \( -name '*.c' -o -name '*.js' -o -name '*.py' \) | while read -r file; do
    first_line=$(head -n 1 "$file")
    case "$first_line" in
        //*|/\**|\#*)
            echo "[+] $file — комментарий есть"
            ;;
        *)
            echo "[-] $file — комментария нет"
            ;;
    esac
done
Создаём тестовые файлы:
``` bash
# С комментарием
echo '// C comment
int main() { return 0; }' > test_comment.c

echo '// JS comment
console.log("Hi");' > test_comment.js

echo '# Python comment
print("Hi")' > test_comment.py

# Без комментария
echo 'print("No comment")' > test_nocomment.py
```
Запуск:
chmod +x check_comment.sh
./check_comment.sh .
**Результат:**
```
[+] ./test_comment.c — комментарий есть
[+] ./test_comment.js — комментарий есть
[+] ./test_comment.py — комментарий есть
[-] ./test_nocomment.py — комментария нет
```
