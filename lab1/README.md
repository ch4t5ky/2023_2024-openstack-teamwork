University: [ITMO University](https://itmo.ru/ru/)  \
Faculty: [FICT](https://fict.itmo.ru) \
Year: 2023/2024 \
Group: 
- Заглубоцкий Артём
- Доронин Дмитрий
- Мигулаева Татьяна
- Ишутина Елизавета

# Лабораторная работа №1 "Подготовка к развертыванию OpenStack"
## Введение
Облачная платформа OpenStack имеет ряд требований к окружению, необходимому для её функционирования. Так, большинство сервисов OpenStack используют базу данных SQL для хранения информации. В ней хранятся данные пользователей, проектов, информация о точках входа в модули, информация о запущенных машинах, созданных сетях и образах. База данных обычно располагается на управляющем узле.

Платформа OpenStack имеет набор интерфейсов для взаимодействия c системой со стороны пользователя. В данной работе рассмотрен command line interface (CLI) и модуль Horizon. Рассмотрены возможности авторизации в системе, создания проектов и пользователей.
## Ход выполнения работы
Перед развертыванием OpenStack требуется подготовить виртуальную машину. Для этого используется виртуальная машина основанная на образе Alma Linux3 Minimal, запущенная с использованием ПО Virtual Box со следующими параметрами:

![VirtualBox_4vdATlqV7V](https://github.com/ch4t5ky/2023_2024-openstack-teamwork/assets/141184937/cff1a06d-1eaa-4fd9-96ca-2b9dac3ed572)

Также для виртуальном машины пробрасываются порты для дальнейшего управления по ssh, и доступа к ui openstack:
![VirtualBox_VbV40EMUpc](https://github.com/ch4t5ky/2023_2024-openstack-teamwork/assets/141184937/f9905a68-e09b-4d69-9d7e-b57f2715200c)


После обновления ОС, установки git и клонирования репозитория содержащего скрипты и конфигурацию для настройки системы и установки openstack был изучен и запущен скрипт `prepare.sh`. Суть работы скрипта в:
- Временном отключении и настройке сервисов мешающих работе OpenStack
- Добавлении дополнительного репозиторя для загрузки нужных пакетов-
- Установки OpenStack Yoga (25 релиз)
- Установки packstack (cli утилита для развертывания openstack)
- Генерации файла конфигурации openstack - `answer.cfg`
- Настройке свап файла (при недостатке памяти)
```shell
dnf update
dnf install git
./prepare.sh
```
<details>
<summary>Updating packages and cloning repo</summary>
  
![VirtualBoxVM_ZY2SilglHp](https://github.com/ch4t5ky/2023_2024-openstack-teamwork/assets/141184937/6caa8c24-9c48-40de-9878-b786a1a4d985)

![VirtualBoxVM_CkZI5ezX2F](https://github.com/ch4t5ky/2023_2024-openstack-teamwork/assets/141184937/3841f97e-ca0e-46f2-86a3-c1f72c9b0238)
</details>

Из результатов работы скрипта `prepare.sh` можно увидеть, что шаги были выполнены успешно:
![VirtualBoxVM_5UsF3fxbr4](https://github.com/ch4t5ky/2023_2024-openstack-teamwork/assets/141184937/5bf64296-1ed2-4b24-80c7-7c47032d88ab)

`Из-за неудобства работы с VirtualBox было решено настроить VS Code для работы с ВМ по ssh, и zsh`
```
ssh -p 322 root@localhost
```
<details>
<summary>VS Code setup</summary>

![Code_qk4BI548LY](https://github.com/ch4t5ky/2023_2024-openstack-teamwork/assets/141184937/c96e55a0-22b7-4ea8-8cfd-d2f991c55173)
</details>   

Далее был запущен скрипт `config.sh`, который: 
- Отключает установку части компонентов openstack, а именно: Swift (объектное хранилище), Ceilometer и Aodh (инфраструктура для сбора метрик и работой с метриками), Neutron Metering Agent (сервис для работы с сетью)
- Отключает демо
- Устанавливает размер cinder volumes в 10GB
- Устанавливает ограничение в 1 для количества работающих воркеров
- И настраивает мост для внешней сети

```shell
./config
```

Проведя конфигурацию системы и файла `answer.cfg`, используя утилиту `packstack` происходит развертывание OpenStack, после чего появляется доступ к панели управления OpenStack по адресу `http://localhost:8080/dashboard`:
```shell
packstack --answer-file=answer.cfg 
```

<details>
<summary>Openstack deployment terminal output</summary>

![Code_TeunCFPHSI](https://github.com/ch4t5ky/2023_2024-openstack-teamwork/assets/141184937/ce0a1dd2-d9e6-4723-84f7-ea37cad4e2cc)

![Code_mb4tR4uxA6](https://github.com/ch4t5ky/2023_2024-openstack-teamwork/assets/141184937/e52d5849-bda6-4d37-9317-be7cd33d1f3f)
</details>

![browser_XbaxTl9qNS](https://github.com/ch4t5ky/2023_2024-openstack-teamwork/assets/141184937/6a7a5383-7035-4ff4-a933-da87401ecc00)


Данные для авторизации были взяты из файла `~/keystonerc_admin`, после чего данные были добавле ны в переменные среды командой `source`, и были изучены:
- список эндпоинтов для сервисов Openstack `openstack endpoint list`:
![Code_PRmZglAwek](https://github.com/ch4t5ky/2023_2024-openstack-teamwork/assets/141184937/27dcec4f-8685-4dff-8cca-d739c5ad6a51)

- список пользователей `openstack user list`, из которого было видно, что был создан пользователь для каждого сервиса системы, а также административный пользователь:
![Code_tL5BYA0T6c](https://github.com/ch4t5ky/2023_2024-openstack-teamwork/assets/141184937/9096aa15-1570-4c47-a369-47ba641cfba4)
- список проектов `openstack project list` до, и после создания демо проекта `openstack project create --domain default --description "Demo Project" demo`, в котором было видно, что даже до создания демо присутствовало два проекта - services и admin. Проект admin, используется для управления системой, но наиболее интересный из них - services, потому что этот проект позволяет сервисам общаятся между собой:
![Code_QsSRcrg9aY](https://github.com/ch4t5ky/2023_2024-openstack-teamwork/assets/141184937/d07fa815-4555-45c0-931c-a408162414ae)

Далее в веб-панели был создан проект `sfera` и пользователь проекта `samovar`, а также ему была выдана роль `_member_`:
Панель проектов (на ней можно увидеть и ранее созданный демо проект):
![browser_U8TbOA36Zw](https://github.com/ch4t5ky/2023_2024-openstack-teamwork/assets/141184937/636eb8ae-1c02-4070-95bb-5303ef7813a7)

Создание проекта:   
![browser_GqwFV4ba19](https://github.com/ch4t5ky/2023_2024-openstack-teamwork/assets/141184937/f4e21ac2-1d50-40fc-a83c-777adfdb6610)

Создание пользователя:
![browser_JXrgLtcfju](https://github.com/ch4t5ky/2023_2024-openstack-teamwork/assets/141184937/d19d210b-9093-4c57-9295-4f382bdc592b)
![browser_DTlml5visM](https://github.com/ch4t5ky/2023_2024-openstack-teamwork/assets/141184937/c0aef644-2f4d-4709-b5fd-18d443ffacf1)

После авторизации под новым пользователем можно увидеть, что у него есть доступ только к проекту `sfera`:
![browser_b93WTOH8aD](https://github.com/ch4t5ky/2023_2024-openstack-teamwork/assets/141184937/c82e5e7c-1525-4327-9531-16cc642ca3ba)
