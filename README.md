# Домашнее задание к занятию "`10.4. Резервное копирование`" - `Барановский Станислав`


### Инструкция по выполнению домашнего задания

   1. Сделайте `fork` данного репозитория к себе в Github и переименуйте его по названию или номеру занятия, например, https://github.com/имя-вашего-репозитория/git-hw или  https://github.com/имя-вашего-репозитория/7-1-ansible-hw).
   2. Выполните клонирование данного репозитория к себе на ПК с помощью команды `git clone`.
   3. Выполните домашнее задание и заполните у себя локально этот файл README.md:
      - впишите вверху название занятия и вашу фамилию и имя
      - в каждом задании добавьте решение в требуемом виде (текст/код/скриншоты/ссылка)
      - для корректного добавления скриншотов воспользуйтесь [инструкцией "Как вставить скриншот в шаблон с решением](https://github.com/netology-code/sys-pattern-homework/blob/main/screen-instruction.md)
      - при оформлении используйте возможности языка разметки md (коротко об этом можно посмотреть в [инструкции  по MarkDown](https://github.com/netology-code/sys-pattern-homework/blob/main/md-instruction.md))
   4. После завершения работы над домашним заданием сделайте коммит (`git commit -m "comment"`) и отправьте его на Github (`git push origin`);
   5. Для проверки домашнего задания преподавателем в личном кабинете прикрепите и отправьте ссылку на решение в виде md-файла в вашем Github.
   6. Любые вопросы по выполнению заданий спрашивайте в чате учебной группы и/или в разделе “Вопросы по заданию” в личном кабинете.
   
Желаем успехов в выполнении домашнего задания!
   
### Дополнительные материалы, которые могут быть полезны для выполнения задания

1. [Руководство по оформлению Markdown файлов](https://gist.github.com/Jekins/2bf2d0638163f1294637#Code)

---

## Задание 1

В чём разница между:
- полным резервным копированием,
- дифференциальным резервным копированием,
- инкрементным резервным копированием.

*Приведите ответ в свободной форме.*
```
```
Полное резервное копирование (Full BackUp) - каждый раз выполняется полное копирование всех данных.
- самый надёжный, т.к. каждый раз копируются все данные;
- самый быстрый, с точки зрения скорости восстановления;
- самый медленный и ресурсоемкий, с точки зрения создание и хранения копии.

Дифференциальное резервное копирование (Differential BackUp) - полное резервное копирование делается только первый раз, затем резервируются измененные данные с момента последней полной копии.
- восстановление выполняется медленнее, чем в полном и быстрее, чем в инкрементальном;
- копирование выполняется быстрее, чем в полном и медленне, чем в инкрементальном.

Инкрементное резервное копирование (Incremental BackUp) - похоже на дифифференциальное копирование, за исключением того, что каждая новая копия содержит только данные, изменённые после последней инкрементальной копии.
- самый медленный, с точки зрения скорости восстановления;
- самый быстрый на наименее ресурсоёмкий, с точки зрения создание и хранения копии.

---

## Задание 2

Установите программное обеспечении Bacula, настройте bacula-dir, bacula-sd, bacula-fd. Протестируйте работу сервисов.

*Пришлите конфигурационные файлы для bacula-dir, bacula-sd, bacula-fd.*
```
sudo apt install bacula postgresql
sudo nano /etc/bacula/bacula-sd.conf
sudo /usr/sbin/bacula-sd -t -c /etc/bacula/bacula-sd.conf
sudo systemctl restart bacula-sd.service
sudo systemctl status bacula-sd.service
sudo nano /etc/bacula/bacula-fd.conf # Конфигурим локального клиента
sudo /usr/sbin/bacula-fd -t -c /etc/bacula/bacula-fd.conf
sudo systemctl restart bacula-fd.service
sudo systemctl status bacula-fd.service
sudo nano /etc/bacula/bacula-dir.conf # Конфигурим консоль
sudo /usr/sbin/bacula-dir -t -c /etc/bacula/bacula-dir.conf
sudo systemctl restart bacula-dir.service
sudo systemctl status bacula-dir.service
# Переходим в консоль
sudo bconsole
* run
* message
* exit
```
[**Файл bacula-sd.conf**](https://github.com/StanislavBaranovskii/10-4-hw/blob/main/data/bacula-sd.conf)

[**Файл bacula-fd.conf**](https://github.com/StanislavBaranovskii/10-4-hw/blob/main/data/bacula-fd.conf)

[**Файл bacula-dir.conf**](https://github.com/StanislavBaranovskii/10-4-hw/blob/main/data/bacula-dir.conf)

---

## Задание 3

Установите программное обеспечении Rsync. Настройте синхронизацию на двух нодах. Протестируйте работу сервиса.

*Пришлите рабочую конфигурацию сервера и клиента Rsync.*
```
sudo apt install rsync
sudo nano /etc/default/rsync # RSYNC_ENABLE=true
sudo nano /etc/rsyncd.conf # Содержимое файла ниже в блоке кода
sudo systemctl start rsync.service
sudo systemctl status rsync.service
sudo netstat -tulnp |grep rsync # Проверяем работу rsync по сети
sudo nano /etc/rsyncd.scrt # backup:12345
sudo chmod 0600 /etc/rsyncd.scrt
# На второй ноде
sudo mkdir /root/scripts
sudo nano /root/scripts/backup-node1.sh # Содержимое файла-скрипта ниже в блоке кода
chmod 0744 /root/scripts/backup-node1.sh
sudo nano /etc/rsyncd.scrt # 12345
sudo chmod 0600 /etc/rsyncd.scrt
/root/scripts/backup-node1.sh # Тестируем синхронизацию
```
**Файл rsyncd.conf :**
```
pid file = /var/run/rsyncd.pid
log file = /var/log/rsyncd.log
transfer logging = true
munge symlinks = yes
# папка источник для бэкапа
[data]
path = /data
uid = root
read only = yes
list = yes
comment = Data backup Dir
auth users = backup
secrets file = /etc/rsyncd.scrt
```
[**Файл backup-node1.sh**](https://github.com/StanislavBaranovskii/10-4-hw/blob/main/data/backup-node1.sh)

---

## Задание 4*

Настройте резервное копирование двумя или более методами, используя одну из рассмотренных команд для папки `/etc/default`. Проверьте резервное копирование.

*Пришлите рабочую конфигурацию выбранного сервиса по поставленной задаче.*
```
```

---
