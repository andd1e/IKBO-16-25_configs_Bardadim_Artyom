# task 1

## task

Вывести отсортированный в алфавитном порядке список имен пользователей в файле passwd (вам понадобится grep).


## solution

```bash
cat /etc/passwd | grep -oP "^[^:]+" | sort
```
```
alpm
avahi
bin
daemon
dbus
flatpak
ftp
git
http
mail
named
nobody
polkitd
postgres
root
rtkit
systemd-coredump
systemd-imds
systemd-journal-remote
systemd-network
systemd-oom
systemd-resolve
systemd-timesync
tss
uuidd
y111e
```


# task 2

## task

Вывести данные /etc/protocols в отформатированном и отсортированном порядке для 5 наибольших портов, как показано в примере ниже:

```
[root@localhost etc]# cat /etc/protocols ...
142 rohc
141 wesp
140 shim6
139 hip
138 manet
```


## solution

awk is line-oriented text processor
in general, the syntax is like: `pattern { action }`

```bash
cat /etc/protocols | awk '{print $2, $1}'
```
```
Full #
 
0 hopopt
1 icmp
2 igmp
3 ggp
4 ipv4
5 st
6 tcp
7 cbt
8 egp
9 igp
10 bbn-rcc-mon
11 nvp-ii
12 pup
14 emcon
15 xnet
16 chaos
17 udp
18 mux
19 dcn-meas
20 hmp
21 prm
22 xns-idp
23 trunk-1
24 trunk-2
25 leaf-1
26 leaf-2
27 rdp
28 irtp
29 iso-tp4
30 netblt
31 mfe-nsp
32 merit-inp
33 dccp
34 3pc
35 idpr
36 xtp
37 ddp
38 idpr-cmtp
39 tp++
40 il
41 ipv6
42 sdrp
43 ipv6-route
44 ipv6-frag
45 idrp
46 rsvp
47 gre
48 dsr
49 bna
50 esp
51 ah
52 i-nlsp
54 narp
55 min-ipv4
56 tlsp
57 skip
58 ipv6-icmp
59 ipv6-nonxt
60 ipv6-opts
62 cftp
64 sat-expak
65 kryptolan
66 rvd
67 ippc
69 sat-mon
70 visa
71 ipcv
72 cpnx
73 cphb
74 wsn
75 pvp
76 br-sat-mon
77 sun-nd
78 wb-mon
79 wb-expak
80 iso-ip
81 vmtp
82 secure-vmtp
83 vines
84 iptm
85 nsfnet-igp
86 dgp
87 tcf
88 eigrp
89 ospfigp
90 sprite-rpc
91 larp
92 mtp
93 ax.25
94 ipip
96 scc-sp
97 etherip
98 encap
100 gmtp
101 ifmp
102 pnni
103 pim
104 aris
105 scps
106 qnx
107 a/n
108 ipcomp
109 snp
110 compaq-peer
111 ipx-in-ip
112 vrrp
113 pgm
115 l2tp
116 ddx
117 iatp
118 stp
119 srp
120 uti
121 smp
123 ptp
125 fire
126 crtp
127 crudp
128 sscopmce
129 iplt
130 sps
131 pipe
132 sctp
133 fc
134 rsvp-e2e-ignore
136 udplite
137 mpls-in-ip
138 manet
139 hip
140 shim6
141 wesp
142 rohc
143 ethernet
144 aggfrag
145 nsh
146 homa
147 bit-emu
255 reserved
```


# task 3

## task

Написать программу banner средствами bash для вывода текстов, как в следующем примере (размер баннера должен меняться!):

```
[root@localhost ~]# ./banner "Hello from RTU MIREA!"
+-----------------------+
| Hello from RTU MIREA! |
+-----------------------+
```

Перед отправкой решения проверьте его в ShellCheck на предупреждения.


## solution

banner:
```bash
#!/bin/env bash

input="$1"
len=$(( ${#input} + 2 ))

str=""
for ((i=1; i<=len; i++)); do
    str="${str}-"
done
str="+${str}+"

echo "$str"
echo "| $input |"
echo "$str"
```

```bash
./banner 123
```
```
+-----+
| 123 |
+-----+
```


# task 4

## task

Написать программу для вывода всех идентификаторов (по правилам C/C++ или Java) в файле (без повторений).

Пример для hello.c:
```
h hello include int main n printf return stdio void world
```


## solution

identifier:
```bash
#!/bin/env bash

file="$1"

grep -oP '\b(?!\d\w*)\w+\b' "$file" | xargs
# negative lookahead since \w includes digits and the first char can't be
```

hello.cpp:
```cpp
#include <iostream>

int main()
{
    std::cout << "hello there" << std::endl;

    return 0;
}
```

```bash
identifier hello.cpp
```
```
include iostream int main std cout std endl return
```


# task 5

## task

Написать программу для регистрации пользовательской команды (правильные права доступа и копирование в `/usr/local/bin`).

Например, пусть программа называется `reg`:
```bash
./reg banner
```

В результате для banner задаются правильные права доступа и сам banner копируется в `/usr/local/bin`.


## solution

В качестве командной оболочки была выбрана `fish` за более лаконичный синтаксис и убирание многого шаблонного кода.

```fish
#!/usr/bin/env fish

argparse f/filename= -- $argv
or return 1
if not set -ql _flag_filename
    echo "Error: filename expected" >&2
    return 1
end
set filename _flag_filename

sudo chmod +x "$filename"
sudo mv "$filename" /usr/bin/
```


# task 6

## task

Написать программу для проверки наличия комментария в первой строке файлов с расширением c, js и py.


## solution

```fish
#!/usr/bin/env fish

argparse p/path= -- $argv
or return 1
if not set -ql _flag_path
    echo "Error: path expected" >&2
    return 1
end
set path "$_flag_path"
set extension (path extension "$path")

set single_pattern
set multi_pattern

read -l line < "$path"
switch "$extension"
case '.c' '.js'
    set single_pattern "^\s*//"
    set multi_pattern "^\s*/\*"
case '.py'
    set single_pattern "^\s*#"
    set multi_pattern "^\s*(?:'''|\"\"\")"
end

set single_line_comment "$(string match -r "$single_pattern" "$line")"
set multi_line_comment "$(string match -r "$multi_pattern" "$line")"

if test -n "$single_line_comment" -o -n "$multi_line_comment"
    echo "Starts with a comment"
    return
end

echo "Does not start with a comment"
```


# task 7

## task

Написать программу для нахождения файлов-дубликатов (имеющих 1 или более копий содержимого) по заданному пути (и подкаталогам).


## solution

```fish
#!/usr/bin/env fish

argparse p/path= -- $argv
or return 1

if not set -ql _flag_path
    echo "Error: path expected" >&2
    return 1
end
set path "$_flag_path"

if not test -d "$path"
    echo "Error: '$path' is not a directory" >&2
    return 1
end


set result (find "$path" -type f -exec sha256sum {} + | sort | uniq -dw64)

if test -z "$result"
    echo "No duplicates found"
    return
end

printf "%s\n" $result
```


# task 8

## task

Написать программу, которая находит все файлы в данном каталоге с расширением, указанным в качестве аргумента и архивирует все эти файлы в архив tar.

## solution

```fish
#!/usr/bin/env fish

function extract_archive_name
    set paths $argv
    if test (count $paths) -eq 1
        set basename (path basename "$paths[1]")
        if test -z "$(string replace -r '\.+' '' "$basename")"
            set basename "archive"
        end
        echo "$basename.tar"
        return
    end

    set basename "archive.tar"
    echo "$basename"
end

function collect_files
    set ext $argv[1]
    set paths $argv[2..]

    set files
    for path in $paths
        set files $files (find "$path" -type f -name "*.$ext")
    end
    printf "%s\n" $files
end

function tar_files
    set archive $argv[1]
    set files $argv[2..]

    tar -cf "$archive" -- $files
end


argparse e/extension= a/archive-name= -- $argv
or return 1

if not set -ql _flag_extension
    echo "Error: extension expected" >&2
    return 1
end
set ext "$_flag_extension"

set paths $argv
if test (count $paths) -eq 0
    echo "Error: at least one path expected" >&2
    return 1
end

if set -ql _flag_archive_name
    set archive "$_flag_archive_name"
else
    set archive "$(extract_archive_name $paths)"
    echo "Using default archive name, '$archive'"
end


set files (collect_files "$ext" $paths)
tar_files "$archive" $files
```

```bash
find test_dir_ext
```
```
test_dir_ext
test_dir_ext/a.txt
test_dir_ext/c.md
test_dir_ext/sub
test_dir_ext/sub/b.txt
```

```bash
./archive_ext -e txt -a out.tar test_dir_ext
tar -tf out.tar
```
```
test_dir_ext/sub/b.txt
test_dir_ext/a.txt
```

без флага `-a` имя архива берется из имени каталога:
```bash
./archive_ext -e txt test_dir_ext
```
```
Using default archive name, 'test_dir_ext.tar'
```


# task 9

## task

Написать программу, которая заменяет в файле последовательности из 4 пробелов на символ табуляции. Входной и выходной файлы задаются аргументами.


## solution

replace_spaces:
```fish
#!/usr/bin/env fish

argparse i/input= o/output= -- $argv
or return 1

if not set -ql _flag_input
    echo "Error: input file expected" >&2
    return 1
end
set input "$_flag_input"

if not set -ql _flag_output
    echo "Error: output file expected" >&2
    return 1
end
set output "$_flag_output"

if not test -f "$input"
    echo "Error: '$input' is not a file" >&2
    return 1
end

sed 's/    /\t/g' "$input" > "$output"
```

in.c:
```c
int main()
{
    return 0;
}
```

```bash
./replace_spaces -i in.c -o out.c
cat -A out.c
```
```
int main()$
{$
^Ireturn 0;$
}$
```


# task 10

## task

Написать программу, которая выводит названия всех пустых текстовых файлов в указанной директории. Директория передается в программу параметром.


## solution

```fish
#!/usr/bin/env fish

argparse p/path= -- $argv
or return 1

if not set -ql _flag_path
    echo "Error: path expected" >&2
    return 1
end
set path "$_flag_path"

if not test -d "$path"
    echo "Error: '$path' is not a directory" >&2
    return 1
end

set result (find "$path" -maxdepth 1 -type f -empty -printf "%f\n" | sort)

if test -z "$result"
    echo "No empty files found"
    return
end

printf "%s\n" $result
```

```bash
./find_empty -p test_dir_empty
```
```
e1
e2
```

```bash
./find_empty -p test_dir_empty/sub
```
```
No empty files found
```
