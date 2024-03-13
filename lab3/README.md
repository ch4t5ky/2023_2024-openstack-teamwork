University: [ITMO University](https://itmo.ru/ru/)  \
Faculty: [FICT](https://fict.itmo.ru) \
Year: 2023/2024 \
Group:

- Заглубоцкий Артём
- Доронин Дмитрий
- Мигулаева Татьяна
- Ишутина Елизавета

# Лабораторная работа №3 "Работа с Openstack API"

## Содержание


## Ход работы

## Выполнение задания

### Задание

## Ответы на вопросы

> Какие протоколы тунеллирования использует Neutron?

Generic routing encapsulation (GRE), Virtual extensible local area network (VXLAN), Generic Network Virtualization Encapsulation (GENEVE)

> Можно ли заменить Cinder, например, CEPH-ом? Для чего если да, почему если нет?

Cinder — это служба блочного хранилища (томов) OpenStack. Ceph — это популярная распределенная система хранения, разработанная для обеспечения производительности, надежности и масштабируемости, поэтому использовать её может быть полезнее. CEPH можно использовать как backend для Cinder, а также можно загружать каждую виртуальную машину внутри Ceph напрямую, без использования Cinder.