Задание 1. Настройте доменный контроллер Samba на машине BR-SRV.

*

Groupadd hq

Useradd -G hq user1.hq …

Useradd -G hq user5.hq

*

Hostnamectl set-hostname br-srv.hq.alt

Domainname hq.alt

*

Usermod -aG sudo hq.alt//hq

*

Vim /etc/sudoers

%hq ALL=(ALL) NOPASSWD: /bin/cat, /bin/grep, /usr/bin/id

*

Проверка:

Cat /etc/shadow

Grep root /etc/passwd

Apt update

Ls /root
