# task 1

Вывести отсортированный в алфавитном порядке список имен пользователей в файле passwd (вам понадобится grep).

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

Вывести данные /etc/protocols в отформатированном и отсортированном порядке для 5 наибольших портов, как показано в примере ниже:

```
[root@localhost etc]# cat /etc/protocols ...
142 rohc
141 wesp
140 shim6
139 hip
138 manet
```


