# ilya8008_infra
ilya8008 Infra repository

Build status:
master branch: [![Build Status](https://travis-ci.com/Otus-DevOps-2018-09/ilya8008_infra.svg?branch=master)](https://travis-ci.com/Otus-DevOps-2018-09/ilya8008_infra)

## Домашнее задание 3

### Данные для подключения

bastion_IP = 35.205.99.171
someinternalhost_IP = 10.132.0.3

### Подключение к someinternalhost в одну команду

```ssh -A -i /Users/ilya/ilya8008.pem -J ilya8008@35.205.99.171 ilya8008@10.132.0.3```

### Подключение с помощью команды ssh someinternalhost
Добавляем настройки в файл ~/.ssh/config

```
Host someinternalhost
HostName 10.132.0.3
User ilya8008
IdentityFile ~/ilya8008.pem
AddKeysToAgent yes
ProxyJump ilya8008@35.205.99.171
```
Подключаемся при помощи команды ssh someinternalhost

## Домашнее задание 4

### Данные для подключения

testapp_IP = 35.240.32.147 
testapp_port = 9292

### Команда для создания инстанса с использованием startup script

```
gcloud compute instances create reddit-app\
  --boot-disk-size=10GB \  
  --image-family ubuntu-1604-lts \
  --image-project=ubuntu-os-cloud \
  --machine-type=g1-small \
  --tags puma-server \
  --restart-on-failure \
  --metadata-from-file startup-script=startup_script.sh
```

### Команда для создания правила firewall

```
gcloud compute firewall-rules create default-puma-server\
  --allow tcp:9292 \
 --source-ranges 0.0.0.0/0 \
 --target-tags puma-server
```

## Домашнее задание 5

### Сделано:

 - Установил packer
 - Установил ADC
 - Создал шаблон для packer
 - Создал скрипты для провижининга
 - Создал образ при помощи packer
 - Проверил образ, создал VM из образа и вручную выполнил deploy приложения
 - Добавил в шаблон пользовательские переменные
 - Запек в образ все зависимости и приложение, проверил работу
 - Создал скрипт для запуска VM из командной строки

## Домашнее задание 6

### Сделано:

 - Установил terraform
 - Создал конфигурационный файл main.tf и проверил создание инстанса
 - Добавил в настройки инстанса ключ для подключения через ssh
 - Создал файл outputs.tf c описанием выходных переменных
 - Определил правило firewall для приложения
 - Добавил тег для истанса
 - Добавил описание провижинеров и проверил их работу
 - Создал файл variables.tf с описанием входных переменных
 - Добавил ключи нескольких пользователей в метаданные проекта
 - Добавил в веб-интерфейсе ssh-ключ пользователю appuser_web. После выполнения terraform apply ключ пользователя appuser_web был удален. Т.о., если мы управляем ключами через terraform, нельзя их добавлять вручную через веб-интерфейс.
 - Создал файл lb.tf и описал в нем настройки балансировщика. Добавил в код второй инстанс и output переменные балансировщика и второго инстанса, проверил работу балансировщика при отказе одного из инстансов. При ручном описании повторяющихся инстансов возникают проблемы с повтром кода, масштабированием приложения и повышается вероятность ошибок.
 - Переписал описание инстанса с использованием параметра count и добавил соответствующую переменную. Проверил создание инстансов и работу балансировщика.

## Домашнее задание 7

### Сделано:

 - Настроил правило firewall для подключения через ssh, импортировал информацию о ранее созданном правиле в state-файл.
 - Изучил взимосвязи ресурсов, задал ресурс IP-адреса.
 - Создал раздельные образы для базы данных и для приложения. Внес изменения в конфигурационный файл mongodb при создании образа ( по-умолчанию mongodb работает только на localhost)
 - Создал отдельные конфиги terraform для БД и приложения
 - Вынес в отдельный конфиг правило firewall для ssh.
 - Создал модули bd, app и vpc. Настроил outputs.tf для работы с модулями.
 - Выполнил параметризацию модуля vpc и проверил работу.
 - Создал инфраструктуру для stage и prod.
 - Изучил работу модуля storage-bucket.
 - Настроил хранение state фалов для stage и prod в gcs, проверил, что terraform использует именно gcs. Проверил работу блокировки: при одновременном запуске terraform из разных директорий получил ошибку.
 - Настроил provision приложения. Настроил передачу адреса (из внутренней сети, иначе правило firewall не работает с тегом) в unit-файл puma.service. Проверил работу приложения (в т.ч. соединение с отдельным инстансом БД).

## Домашнее задание 8

### Сделано:

 - Установил ansible.
 - Создал inventory в различных форматах, ansible.cfg
 - Проверил выполнение команд с помощью модулей command и shell.
 - Проверил работу модулей systemd,service и git.
 - Написал playbook. После удаления репозитория с помощью модуля command и повторного выполнения playbook статус задания изменился на changed, т.к. ansible повторно сделал clone репозитория.  
```
TASK [Clone repo]
changed: [appserver]

PLAY RECAP
appserver                  : ok=2    changed=1    unreachable=0    failed=0
```
 - Создал файл inventory.json в предлогаемом формате. Для использования файла написал скрипт, который выводит данный файл в stdout. Команда для проверки:
``` ansible all -m ping -i invent.sh ```

## Домашнее задание 9

### Сделано:

 - Создал и проверил работу плейбука с одним сценарием (использовал шаблон конфига для mongodb, тэги, хендлеры, шаблон для передачи переменной в unit puma и написал таски для деплоя приложения).
 - Создал и проверил работу плейбука с несколькими сценариями (установил необходимые группы хостов, тэги и другие параметры на уровне сценариев).
 - Создал и проверил работу нескольких плейбуков (удалил тэги, сделал отдельные плейбуки db.yml, app.yml и deploy.yml, создал главный плейбук site.yml).
 - Исследовал работу с dynamic inventory. Для получения json файла выбрал terraform-inventory. Т.к. у выбранной утилиты наблюдаются проблемы с remote backend, написал скрипт gce-invent.sh и прописал его в опции inventory в ansible.cfg. В app.yml я использую переменную db_internal_ip из dynamic inventory для записи в шаблон db_config.
 - Заменил провижининг в packer на плейбуки ansible и создал новые образы.
 - Проверил правильность создания инстансов и работы приложения со всеми изменениями.

## Домашнее задание 10

### Сделано:

- Создал роль для базы данных.
- Создал роль для приложения.
- Добавил вызов ролей в ранее созданные плейбуки и проверил их работу.
- Создал настройки окружений для stage и prod и определил окружение stage по умолчанию.
- Определил переменные групп хостов для окружений.
- Добавил таски для вывода информации о текущем окружении.
- Организовал директорию ansible согласно best practices.
- Добавил вывод изменений в ansible.cfg.
- Проверил работу приложения в окружениях stage и prod со всеми внесенными изменениями.
- Использовал community роль jdauphant.nginx для настройки обратного прокси, добавил правило firewall и проверил доступность приложения на 80 порту.
- Изучил работу с Ansible Vault и проверил работу плейбука для добавления пользователей с зашифрованными файлами.
- Настроил скрипты для dynamic inventory для работы c окружениями stage и prod
- Настроил TravisCI для выполнения следующих действий:
  - packer validate
  - terraform validate
  - tflint
  - ansible-lint
- Для отладки, настроил и использовал утилиту trytravis.
- Добавил в README.md бейдж со статусом билда ветки master.

## Домашнее задание 11

### Сделано:

- Установил Vagrant на локальный ПК.
- Описал в файле Vagrant виртуалки и проверил их создание и работы.
- Добавил провижининг в определение хостов.
- Доработал роли app и db для работы с Vagrant
- Параметризировал конфигурацию для возможности работы с пользователем, отличным от appuser.
- Переопределил переманные с помощью extra_vars.
- Проверил создание виртуалок и работу приложения.
- Дополнил extra_vars для работы проксирования nginx и проверил работу.
- Установил molecule и testinfra.
- Настроил тестирование роли db, проверил создание тестовой машины и прохождение тестов.
- Написал тест для проверки доступности БД на порту 27017, проверил возникновение ошибки при изменении номера порта.
- Доработал роли для packer и шаблоны packer, проверил корректность создания образов.
