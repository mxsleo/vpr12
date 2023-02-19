# Лабораторная работа #4 - sh скрипты


## Работа

### 1. Объясните, как работают программы (+назовите синтаксические конструкции языка Shell-сценариев, требующих понимания их структуры):

**Листинг 1 – вывод позиционных параметров**
```sh
#/bin/sh            # Комментарий означает путь до интерпритатора
echo скрипт $0      # 'echo' - вывод в консоль "скрипт $0" - где $0 - 0й арг - он же имя исп файла (см пример).
echo $1 $2 $3       # Вывод 1, 2, 3-го позиц. арг.
shift               # сдвиг влево на 1 арг (т.к арг команды не указан)
echo $1 $2 $3       # Вывод 1, 2, 3-го позиц. арг.
```
Пример:
```bash
>>> ./l1.sh 1 2 3 4
скрипт ./l1.sh
1 2 3
2 3 4
```
Причем наш скрипт изначально не содержал `$4` - прямое использовние 4го арг-та, однако `shift` выполнило сдиг


**Листинг 2 – анализ количества входных параметров**
```sh
#!/bin/sh
if [ $# -lt 2 ]                   # Если не обнаружены: 1вый и 2й позиц. аргументы
then                              # То
	echo usage: $0 arg1 arg2        # Вывод: "usage: $0 arg1 arg2"
	exit 1                          # Выход с кодом 1
fi                                #
```
Примеры:
```bash
>>> ./l2.sh
echo usage: $0 arg1 arg2
```
```bash
>>> ./l2.sh 1
echo usage: $0 arg1 arg2
```
```bash
>>> ./l2.sh 1 2

```

**Листинг 3 – поиск и визуализация результата**
```sh
#/bin/sh
grep user1 /etc/passwd		# Поиск всех строк в файле '/etc/passwd', в которых встречается слово user1
if [ $? -ne 0 ]			# Если 0
then
	echo пользователь user1 в системе не зарегистрирован
fi
```
Пример:
```bash
>>> ./l3.sh
пользователь user1 в системе не зарегистрирован
```

**Листинг 4 – вывод содержимого временного файла**
```sh
#!/bin/sh
tempfile=/tmp/tmp.$$		# Переменной 'tempfile' присвоено значение '/tmp/tmp.$$' (путь к времен файлу)
touch $tempfile			# Создать пустой файл на (знач переменной 'tempfile')
cat $tempfile			# Вывести содержимое файла на (знач переменной 'tempfile')
rm  $tempfile			# Удалить файл на (знач переменной 'tempfile')
```
Пример:
```bash
>>> ./l4.sh

```

### 2. Составьте и выполните shell - программы, включающей следующие действия: 

**1) Вывод в терминал списка параметров командной строки c указанием номера каждого параметра.**
```sh
#!/bin/sh
for i in $*
do
    echo "Арг: " $i
done
```
Запуск:
```bash
>>> ./1.sh 1 2 3 4 wef
Арг:  1
Арг:  2
Арг:  3
Арг:  4
Арг:  wef
```

**2) Присвоение переменным А, В и С значений 10, 100 и 200, вычисление и вывод числового значения правой части уравнения D=(A\*2 + B/3)\*C.**

```sh
#!/bin/sh
A=10
B=100
C=200
D=$(( ($A*2 + $B/3) * $C ))
echo $D

```
Запуск:
```bash
>>> ./2.sh
10600
```

**3) Запись списка файлов зарегистрированных домашнем каталоге в отдельный файл, вывод в терминал этого списка в алфавитном порядке и общего количества файлов.**
```sh
#/bin/sh
tempfile=/tmp/tmp.$$
touch $tempfile

ls -d ~/* >> $tempfile
cat $tempfile

count_of_lines=$( cat $tempfile | wc -l)
echo "Всего файлов: $count_of_lines"
rm  $tempfile

```
Запуск:
```bash
>>> ./3.sh 
/home/jkearnsl/IdeaProjects
/home/jkearnsl/PycharmProjects
/home/jkearnsl/snap
/home/jkearnsl/wireguardsaved.zip
/home/jkearnsl/Видео
/home/jkearnsl/Документы
/home/jkearnsl/Загрузки
/home/jkearnsl/Изображения
/home/jkearnsl/Музыка
/home/jkearnsl/Общедоступные
/home/jkearnsl/Рабочий стол
/home/jkearnsl/Шаблоны
Всего файлов: 12
```
Немного о том, как это работает:

В начале, как и в п.4 (вывод содержимого файла) мы создаем временный файл: 
```sh
tempfile=/tmp/tmp.$$
touch $tempfile
```
Далее командой `ls -d ~/*` выводим содержимое каталога `~` (домашний каталог текущего пользователя) и всех файлов этого каталога `/*`,
и записываем вывод `>>` в файл `$tempfile`. После чего просто выводим содержимое файла `cat $tempfile`
```sh
ls -d ~/* >> $tempfile
cat $tempfile
```
С помощью команды `cat $tempfile | wc -l` считываем кол-во строк в файле `$tempfile`. Аналогичный синтаксис команды `wc`: `wc -l filename.txt`
выводим сообщение и удаляем файл.

**4) Переход в другой каталог, формирование файла c листингом каталога и возвращение в исходный каталог.**

```sh
#/bin/sh
current_dir=$(pwd)
# Создание директории
mkdir somenew_dir
next_dir="$current_dir/somenew_dir"
# Создание файла
cd "$next_dir"
touch some_info.txt
# Заполнение информацией
ls -d "$current_dir"/* >> "$next_dir/some_info.txt"
cd ../
```
Запуск:
```bash
>>> ./4.sh
>>> cat somenew_dir/some_info.txt 
/home/jkearnsl/Рабочий стол/лаб ос/лаб 4/1.sh
/home/jkearnsl/Рабочий стол/лаб ос/лаб 4/2.sh
/home/jkearnsl/Рабочий стол/лаб ос/лаб 4/3.sh
/home/jkearnsl/Рабочий стол/лаб ос/лаб 4/4.sh
/home/jkearnsl/Рабочий стол/лаб ос/лаб 4/Laboratornaya_rabota4 (1).docx
/home/jkearnsl/Рабочий стол/лаб ос/лаб 4/some_info.txt
/home/jkearnsl/Рабочий стол/лаб ос/лаб 4/some.md
/home/jkearnsl/Рабочий стол/лаб ос/лаб 4/somenew_dir
```

**5) Запрос и ввод имени пользователя, сравнение c текущим логическим именем пользователя и вывод сообщения: верно/неверно.**

```sh
#/bin/sh
current_user=$(whoami)
if [ $# -lt 1 ]
then
	echo Используйте: $0 username
	echo Чтобы получить булевое True/False - текущий ли это пользователь или нет
	exit 1
fi

if [ $current_user == $1 ]
then
    echo True
else
    echo False
fi
exit 0

```
Запуск:
```bash
>>> ./5.sh
Используйте: ./5.sh username
Чтобы получить булевое True/False - текущий ли это пользователь или нет
```bash
>>> ./5.sh jk
False
```bash
>>> ./5.sh jkearnsl
True
```

**6) Запрос и ввод имени файла в текущем каталоге и вывод.**

PS: Насколько я понимаю, от нас просят проверить, существует ли файл с таким именем в этом каталоге

```sh
#/bin/sh
if [ $# -lt 1 ]
then
	echo Используйте: $0 имя_файла
	echo Чтобы получить булевое True/False - существует ли "имя_файла" в данном каталоге
	exit 1
fi
FILE="$(pwd)/$1"

if [ -f "$FILE" ];
then
    echo True
else
	echo False
fi
```
Запуск:
```bash
>>> ./6.sh
Используйте: ./6.sh имя_файла
Чтобы получить булевое True/False - существует ли имя_файла в данном каталоге
```
```bash
>>> ./6.sh 6.some
False
```
```bash
>>> ./6.sh 6.sh
True
```

### 3. Выполните действия (дайте команды) в операционной системе, позволяющие ответить на вопросы:

**1) Выясните, чем отличается реакция операционной системы на различные ошибки аутентификации.**
Ответ:
```
Feb 18 19:31:07 jkearnsl unix_chkpwd[38899]: password check failed for user (jkearnsl)
Feb 18 19:31:07 jkearnsl kcheckpass[38897]: pam_unix(kde:auth): authentication failure; logname= uid=1000 euid=1000 tty=:0 ruser= rhost=  user=jkearnsl
Feb 18 19:31:07 jkearnsl kcheckpass[38897]: Authentication failure for jkearnsl (invoked by uid 1000)
Feb 19 09:57:36 jkearnsl polkitd(authority=local): Operator of unix-session:3 FAILED to authenticate to gain authorization for action org.freedesktop.policykit.pkexec.wireguird for unix-process:2167:4662 [/bin/sh /usr/local/bin/wireguird] (owned by unix-user:jkearnsl)
```
PS: я не знаю как ответить на этот странный вопрос. Ну вот, являются логами (взял из `/var/log/auth.log`)

**2) Сравните права доступа к директориям /bin и /tmp. Какие операции сможет совершать в них простой пользователь?**

Посмотреть разрешения можно, выполнив следующую команду:
```bash
>>> ls -l *путь*
```

* `/bin`

Т.к `/bin` - ссылка на `/usr/bin`, то корректно будет использовать этот путь
```bash
>>> ls -ld /usr/bin
drwxr-xr-x   2 root root 65536 фев 18 11:17 bin
```
Нас интересует `drwxr-xr-x` и `root root`

- `d` - означает что это директория
- `rwx` - владелец (он же `root`) может читать/писать/выполнять
- `r-x` - остальные из группы `root` могут читать и выполнять
- `r-x` - остальные группы могут читать и выполнять

Мы не являемся `root` и не входим в эту группу (по заданию просто пользователи)

=> Ответ: пользователь может просматривать этот каталог и выполнять программы оттуда


* `/tmp`
```bash
>>> ls -ld /tmp
drwxrwxrwt  24 root root  4096 фев 19 13:16 tmp
```
- `d` - означает что это директория
- `rwx` - повторяется 3 раза, => все могут читать/писать/выполнять из `tmp`
- 
=> Ответ: Пользователь также может читать/писать/выполнять


**3) Создайте текстовый файл и задайте права на него таким образом, чтобы он мог просматриваться только владельцем и никем не мог редактироваться (команда chmod).**
Ответ:

Создадим файл:
```bash
>>> touch some.txt
```
Посмотрим права:
```bash
>>> ls -l some.txt
-rw-rw-r-- 1 jkearnsl jkearnsl 0 фев 19 13:31 some.txt
```
Заберем у групп и остальных пользователей все права на файл:
```bash
>>> chmod go-rwx some.txt
```
PS: арг `go-rwx` означает: `group and other -rwx`

Заберем у владельца доступ на запись:
```bash
>>> chmod u-w some.txt
```
Итоговый результат: 
```bash
>>> ls -l some.txt
-r-------- 1 jkearnsl jkearnsl 0 фев 19 13:50 some.txt
```

**4) Что смогут делать другие пользователями c файлами в домашней директории пользователя, если он задаст всем остальным пользователям право на запись в директорию, но удалит право исполнения на неё?**
Ответ:

Если другие пользователи не входят в группу `*имя пользователя*`, то другие пользователи смогут создавать файлы в этой диретории, но не смогут читать эту директорию.

**5) Найдите все исполняемые файлы c установленным suid-битом (команда find . -perm /4000).**

***SUID** — это специальное разрешение файла для исполняемых файлов, которое позволяет другим пользователям запускать файл с эффективными разрешениями владельца файла. Вместо обычного x, который представляет разрешения на выполнение, вы увидите специальное разрешение s (для указания SUID) для пользователя.*

Выполним:
```bash
>>> find /opt -perm /4000
/opt/zoom/cef/chrome-sandbox
/opt/google/chrome/chrome-sandbox
```
В директории `/opt` мы нашли такие файлы :)

**6) Получите имена всех пользователей системы, y которых в качестве командной оболочки используется программа /bin/false.**

Вспомним `grep`, который использовали выше в `sh` скрипте:

Выполним:
```bash
>>> grep /bin/false /etc/passwd
tss:x:106:112:TPM software stack,,,:/var/lib/tpm:/bin/false
whoopsie:x:116:124::/nonexistent:/bin/false
sddm:x:119:128:Simple Desktop Display Manager:/var/lib/sddm:/bin/false
hplip:x:122:7:HPLIP system user,,,:/run/hplip:/bin/false
swtpm:x:125:137:virtual TPM software stack,,,:/var/lib/swtpm:/bin/false
```
В файле `/etc/passwd` мы нашли следующие строки, которые содержат `/bin/false`
