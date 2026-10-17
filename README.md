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
``` bash
nano check_comment.sh
```
Содержимое:
``` bash
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
```
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
``` bash
chmod +x check_comment.sh
./check_comment.sh .
```
**Результат:**
```
[+] ./test_comment.c — комментарий есть
[+] ./test_comment.js — комментарий есть
[+] ./test_comment.py — комментарий есть
[-] ./test_nocomment.py — комментария нет
```
## Задача 7
**Условие:** Написать программу для нахождения файлов-дубликатов (имеющих 1 или более копий содержимого) по заданному пути (и подкаталогам).

**Решение:**
Создаём скрипт:
nano duplicates
``` bash
#!/bin/bash
# Поиск файлов-дубликатов по содержимому

dir="${1:-.}"

find "$dir" -type f -exec md5sum {} + 2>/dev/null \
    | sort \
    | awk '
        {
            hash = $1
            $1 = ""
            sub(/^ /, "")
            if (hash == prev) {
                if (!printed) {
                    print prev_file
                    printed = 1
                }
                print $0
            } else {
                printed = 0
            }
            prev = hash
            prev_file = $0
        }'
```
Текстовая структура:
```bash
mkdir -p test_dup/sub

echo "duplicate content" > test_dup/file1.txt
echo "duplicate content" > test_dup/file2.txt
echo "duplicate content" > test_dup/sub/file3.txt
echo "unique content"    > test_dup/unique.txt
```
Запуск:
``` bash
./duplicates test_dup
```
**Результат:**
``` 
test_dup/file1.txt
test_dup/file2.txt
test_dup/sub/file3.txt
```
## Задача 8
**Условие:** Написать программу, которая находит все файлы в данном каталоге с расширением, указанным в качестве аргумента и архивирует все эти файлы в архив tar.

**Решение:**
Создаём скрипт:
``` bash
nano archive_ext.sh
```
Содержимое:
``` bash
#!/bin/bash
if [ $# -ne 2 ]; then
    echo "Использование: $0 <каталог> <расширение>" >&2
    exit 1
fi

dir="$1"
ext="$2"
archive="archive_${ext}_$(date +%Y%m%d_%H%M%S).tar"

find "$dir" -type f -name "*.$ext" -print0 \
    | tar --null -cvf "$archive" -T -

echo "Архив создан: $archive"
```
Создаем текстовые файлы:
``` bash
mkdir -p test_arch
echo "log 1" > test_arch/app.log
echo "log 2" > test_arch/error.log
echo "log 3" > test_arch/access.log
echo "text"  > test_arch/readme.txt
```
Запуск:
``` bash
chmod +x archive_ext.sh
./archive_ext.sh test_arch log
```
**Результат:**
test_arch/app.log
test_arch/error.log
test_arch/access.log
Архив создан: archive_log_20241015_143022.tar
Содержимое архива:
``` bash
tar -tf archive_log_*.tar
```
## Задача 9
**Условие:** Написать программу, которая заменяет в файле последовательности из 4 пробелов на символ табуляции. Входной и выходной файлы задаются аргументами.

**Решение:**
Создаём скрипт:
``` bash
nano spaces2tab.sh
```
Содержимое:
``` bash
#!/bin/bash
if [ $# -ne 2 ]; then
    echo "Использование: $0 <входной_файл> <выходной_файл>" >&2
    exit 1
fi

input="$1"
output="$2"

if [ ! -f "$input" ]; then
    echo "Ошибка: файл '$input' не найден" >&2
    exit 1
fi

sed 's/    /\t/g' "$input" > "$output"
echo "Готово: $output"
```
Создаём входной файл:
``` bash
printf 'line 1\n    indented line\n        double indented\n' > input.txt
```
Запуск:
``` bash
chmod +x spaces2tab.sh
./spaces2tab.sh input.txt output.txt
```
**Результат:**
До:
``` bash
cat -A output.txt
```
```
line 1$
    indented line$
        double indented$
```
После:
``` bash
cat -A output.txt
```
```
line 1$
    indented line$
        double indented$
```
## Задача 10
**Условие:** Написать программу, которая выводит названия всех пустых текстовых файлов в указанной директории. Директория передается в программу параметром. 

**Решение:**
Создаём скрипт:
``` bash
nano empty_text.sh
```
Содержимое:
``` bash
#!/bin/bash
if [ $# -ne 1 ]; then
    echo "Использование: $0 <директория>" >&2
    exit 1
fi

dir="$1"

if [ ! -d "$dir" ]; then
    echo "Ошибка: '$dir' не является директорией" >&2
    exit 1
fi

find "$dir" -maxdepth 1 -type f -empty \
    \( -name '*.txt' -o -name '*.md' -o -name '*.log' -o -name '*.csv' \) \
    -printf '%f\n'
```
Создание текстовой директории:
``` bash
mkdir -p test_empty

touch test_empty/empty1.txt
touch test_empty/empty2.md
touch test_empty/empty3.log
touch test_empty/empty.bin       # не текстовый
echo "data" > test_empty/full.txt # не пустой
```
Запуск:
``` bash
chmod +x empty_text.sh
./empty_text.sh test_empty
```
**Результат:**
```
empty1.txt
empty2.md
```
empty3.log
