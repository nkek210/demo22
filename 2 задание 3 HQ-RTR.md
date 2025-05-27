# Задание 3.Настройте службу сетевого времени на базе сервиса chrony

* Скачиваем пакет chrony:
  
apt-get install -y chrony

* Запускаем и добавляем в автозагрузку утилиту chronyd:

systemctl enable --now chronyd

* Проверка конфигурации NTP-сервера

chronyc sources

* Приводим начало файла /etc/chrony.conf к следующему виду:
  
#Use public servers from the pool.ntp.org project.

#Please consider joining the pool (https://www.pool.ntp.org/join.html

#pool pool.ntp.org iburst

server 127.0.0.1 iburst prefer

hwtimestamp *

local stratum 5

allow 0/0

* Запускаем и добавляем в автозагрузку утилиту chronyd:
  
systemctl enable --now chronyd

* Проверка конфигурации NTP-сервера
  
chronyc sources

* Получаем вывод уровня стратума с помощью связки команд:
  
chronyc tracking | grep Stratum

* Конфигурация NTP-клиента EcoRouter
  
Указываем IP-адрес NTP-сервера:

ntp server 172.16.4.1

Указываем часовой пояс:

ntp timezone utc+3

Проверка конфигурации NTP-клиента EcoRouter

Проверяем командой:

show ntp status

* Конфигурация NTP-клиента Alt Linux
* 
Скачиваем пакет chrony:

apt-get install chrony

* Приводим начало файла /etc/chrony.conf к следующему виду:
  
#pool pool.ntp.org iburst

server 172.16.4.1 iburst prefer

* Запускаем утилиту chrony и добавляем ее в автозагрузку:
  
systemctl enable --now chronyd

* Проверка конфигурации NTP-клиента Alt Linux
  
chronyc sources
