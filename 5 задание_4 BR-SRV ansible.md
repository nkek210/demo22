# Задание 4. Сконфигурируйте ansible на сервере BR-SRV

Vim /etc/openssh/sshd_config

*

Port 2024

MaxAuthTries 2

PubkeyAuthentication yes

PasswordAuthentication yes

Banner /etc/openssh/bannermotd

AllowUsers  sshuser

* Конфигурация Ansible
  
Устанавливаем необходимые пакеты:

apt-get install -y ansible sshpass

Редактируем указанные строки в конфигурационном файле /etc/ansible/ansible.cfg:

Vim /etc/ansible/ansible.cfg

“ inventory = ./inventory.yml

host_key_checking = False”

Далее заполняем инвентарный файл /etc/ansible/inventory.yml:

all:

  children:
  
    Networking:
    
      hosts:
      
        hq-rtr:
        
        br-rtr:
        
    Servers:
    
      hosts:
      
        hq-srv:
        
          ansible_host: 192.168.100.62
          
          ansible_port: 2024
          
    Clients:
    
      hosts:
      
        hq-cli:
        
          ansible_host: 192.168.200.14
          
          ansible_port: 2024

* Создаем файлы с переменными для всех категорий и для категории Networking:
  
cd /etc/ansible

mkdir group_vars

touch group_vars/{all.yml,Networking.yml}

Редактируем их:

ansible_ssh_user: sshuser

ansible_ssh_pass: P@ssw0rd

ansible_python_interpreter: /usr/bin/python3

ansible_connection: network_cli

ansible_network_os: ios

* Выполняем команду для ping`а всех машин:
  
ansible -m ping all
