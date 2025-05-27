# Задание 9.  Установите приложение Яндекс Браузере для организаций на HQ-CLI

*** сначала

Cat /etc/apt/apt.conf

Mcedit /etc/apt/apt.conf

Acquire::http::Proxy “http:/10.0.21.52:3128/”;

*

Apt-get update

Apt-get install yandex-browser
