University: [ITMO University](https://itmo.ru/ru/)  \
Faculty: [FICT](https://fict.itmo.ru) \
Year: 2023/2024 \
Group:

- Заглубоцкий Артём
- Доронин Дмитрий
- Мигулаева Татьяна
- Ишутина Елизавета

# Лабораторная работа №2 "Создание ВМ"

## Содержание

- [Ход работы](#ход-работы)
  - [Публичные и приватные сети Neutron](#публичные-и-приватные-сети-neutron) 
  - [Флейвор Glance](#флейвор-glance)
  - [Блочное устройство Cinder](#блочное-устройство-cinder)
  - [Рабочий инстанс Nova](#рабочий-инстанс-nova)
- [Выполнение задания](#выполнение-задания)
  - [Задание](#задание)
  - [Создание приватной сети](#1-создание-приватной-сети-через-horizon)
  - [Создание копии блочного устройства](#2-создание-копии-блочного-устройства)
  - [Создание виртуальной машины](#3-создание-виртуальной-машины)
- [Ответы на вопросы](#ответы-на-вопросы)

## Ход работы

### Публичные и приватные сети Neutron

Создание сети провайдера:
![provaider.png](./img/creation_network.png)

Создание подсети в сети провайдера:
![subnet.png](./img/creation_subnet.png)

Создание приватной сети и подсети:
![private_network.png](./img/creation_private_network.png)

Создание роутера:
![router.png](./img/creation_router.png)

### Флейвор Glance

Flavor в Openstack используется для того, чтобы определить вычислительную мощность, объем памяти и размер хранилища для инстанса Nova. \
Создание флейвора виртуальной машины: \
![flavor.png](./img/creation_flavor.png)

### Ключ доступа

Создание ключа доступа для обеспечения возможности подключения без использования пароля:
![key.png](./img/creation_sshkey.png)

### Блочное устройство Cinder

Установка образа Cirros:
![image.png](./img/creation_image.png)

Создание блочного устройства из образа: \
![block_device.png](./img/creation_block_device.png)

### Рабочий инстанс Nova

Для создания рабочего инстанса необходимо перейти в `Вычислительные ресурсы->Инстансы`. Далее нажать на кнопку запустить инстанс, после чего появится диалоговое окно для настройки конфигурации. После того, как будут выбраны все параметры, запрос на создание виртуальной машины будет отправлен в Nova.
![instance.png](./img/created_instance.png)

Проверка IP-адреса виртуальной машины:
![result-ip.png](./img/result_of_ip.png)

## Выполнение задания

### Задание

1. Через Horizon создать еще одну приватную сеть+подсеть (адресация любая), подключить ее к уже существующему роутеру
2. Через Openstack CLI сделать копию ранее созданного блочного устройства
3. Через Openstack CLI создать еще одну ВМ (`openstack server create...`). Параметры машины:
    - имя произвольное
    - источник: созданная копия диска
    - флейвор: созданный ранее (можно другой, если позволяют ресурсы ВМ)
    - сеть: новосозданная приватная
4. Через Horizon проверить, что все завелось

### 1. Создание приватной сети через Horizon

Для создания новой сети необходимо перейти в `Сеть->Сети` и нажать на кнопку создать сеть. После этого появится диалоговое окно, в котором можно настроить создание приватной сети, подсети, а также добавить детали для создания.

![ui-network-creation.png](./img/ui_network_creation.png)

В результате, в той же вкладке отобразится созданная сеть.

![ui-networks](./img/ui_networks.png)

### 2. Создание копии блочного устройства

Команда `openstack create volume` поддерживает несколько источников создания дисков: image(--image), snapshot(--snapshot), volume(--source).

Для создании из копии ранее созданного блочного устройства было использована два способа: snapshot и volume.

Создание из snapshot: \
![from-snapshot.png](./img/creation_disk_from_snapshot.png)

Создание из volume: \
![from-volume.png](./img/creation_block_from_volume.png)

### 3. Создание виртуальной машины

Для создания виртуальной машины использовались следующие аргументы: `--flavor`, `--volume`, `--network`, `--key-name`. \
В итоге, создание полная команда выглядела следующим образом:
![vm.png](./img/create_instance.png)

Проверка созданного инстанса:
![check-instance.png](./img/ui_check_created_instance.png)

Проверка установленного IP-адреса:
![ip-check.png](./img/result_of_ip_second.png)

## Ответы на вопросы

> Что именно сервис с помощью Keystone проверяет в токене пользователя, когда тот пытается осуществить операцию по отношению к этому сервису?

Сервисы проверяют в Keystone две информации: корректный ли токен и разрешено ли использование сервиса.

> При создании ВМ, Nova первым делом идет в Keystone, проверяет токен и т.д. Как думаете, к эндпоинту какого сервиса Nova идет следом?

После того, как Nova проверит токен, следующим запросом является проверка наличии конфликтов с `nova-database` и при их отсутсвии создает начальную запись в базе данных для нового инстанса. Однако следующим запросом в другой сервис, является запрос в Glance для получения образа вм.    
![Virtual-machine-spawning-sequence-in-OpenStack](https://github.com/ch4t5ky/2023_2024-openstack-teamwork/assets/141184937/337477a7-bf15-4932-a0a1-124443289ee7)