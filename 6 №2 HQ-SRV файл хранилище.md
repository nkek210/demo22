# Задание 2. Сконфигурируйте файловое хранилище:

# HQ-SRV

* Создание RAID
  
Lsblk

Вывод:

sdb  8:16  0  1G  0  disk

sdc  8:32  0  1G  0  disk

sdd  8:48  0  1G  0  disk

* Обнуляем суперблоки для добавленных дисков:
  
mdadm --zero-superblock --force /dev/sd{b,c,d}

Вывод:

wipefs --all --force /dev/sd{b,c,d}

* Удаляем старые метаданные и подпись на дисках:
  
wipefs --all --force /dev/sd{b,c,d}

* Создаем RAID:
  
mdadm --create /dev/md0 -l 5 -n 3 /dev/sd{b,c,d}

/dev/md0 - название RAID после сборки

-l 5 - уровень RAID

-n 3 - количество дисков, из которых собирается массив

/dev/sd{b,c,d} - диски, из которых выполняется сборка

Проверяем:

Lsblk

*

sdb  8:16  0  1G  0  disk

  md0  9:0  0  2G  0  raid5
  
sdc  8:32  0  1G  0  disk

  md0  9:0  0  2G  0  raid5
  
sdd  8:48  0  1G  0  disk

  md0  9:0  0  2G  0  raid5

* Создаем файловую систему из созданного RAID:
  
mkfs -t ext4 /dev/md0

* Создание файла mdadm.conf
  
Создаем директорию для файла:

mkdir /etc/mdadm

* Заполняем файл информацией:
  
echo "DEVICE partitions" > /etc/mdadm/mdadm.conf

mdadm --detail --scan | awk '/ARRAY/ {print}' >> /etc/mdadm/mdadm.conf

* Создание файловой системы и монтирование RAID-массива
  
Создаем директорию для монтирования массива:

mkdir /mnt/raid5

Добавляем строку в /etc/fstab:

/dev/md0  /mnt/raid5  ext4  defaults  0  0

* Монтируем:
  
mount -a

Проверяем монтирование:

df -h

Вывод:

/dev/md0  2.0G  24K  1.9G  1%  /mnt/raid5

* Настройка NFS
  
Устанавливаем пакеты для NFS-сервера:

apt-get install -y nfs-{server,utils}

Создаем директорию для общего доступа:

mkdir /mnt/raid5/nfs

Выдаем права на чтение и запись этой директории:

chmod 766 /mnt/raid5/nfs

Добавляем строку в /etc/exports:

/mnt/raid5/nfs 192.168.200.0/28(rw,no_root_squash)

*

/mnt/raid5/nfs - общий ресурс

192.168.200.0/28 - клиентская сеть, которой разрешено монтирование общего ресурса

rw — разрешены чтение и запись

no_root_squash — отключение ограничения прав root

Экспортируем файловую систему, которую прописали ранее:

exportfs -arv

*

-a - экспортировать все указанные каталоги

-r - повторный экспорт всех каталогов, синхронизируя /var/lib/nfs/etab с /etc/exports и файлами в /etc/exports.d

-v - подробный вывод

* Запускаем и добавляем в автозагрузку NFS-сервер:
  
systemctl enable --now nfs-server

# Либо также на HQ-SRV, либо уже на HQ-CLI

* Настройка клиента
  
Устанавливаем требуемые пакеты для NFS-клиента:

apt-get update && apt-get install -y nfs-{utils,clients}

* Создаем директорию для общего ресурса:

mkdir /mnt/nfs

Выдаем права этой директории:

chmod 777 /mnt/nfs

Добавляем строку в /etc/fstab для автоматического монтирования общего ресурса:

192.168.100.62:/mnt/raid5/nfs  /mnt/nfs  nfs  defaults  0  0

Монтируем общий ресурс:

mount -a

Проверяем монтирование:

df -h

192.168.100.62:/mnt/raid5/nfs  2,0G  0  1,9G  0%  /mnt/nfs
