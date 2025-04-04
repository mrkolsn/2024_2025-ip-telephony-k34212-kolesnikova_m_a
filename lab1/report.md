University: ITMO University

Faculty: PIn

Course: IP-telephony

Year: 2024/2025

Group: K34212

Author: Kolesnikova Maria Alexeevna

Lab: Lab1

Date of create: 04.04.2025

Date of finished: 04.04.2025

# Лабораторная работа №1 "Базовая настройка ip-телефонов в среде Сisco packet tracer"

## Описание
Для выполнения данной лабораторной работы собирается схема соединения. Необходимо проверить, правильно ли подключены и настроены все узлы устройств.

## Цель работы
Изучить рабочую среду Cisco Packet Tracer, ознакомить- ся с интерфейсами основных устройств, типами кабелей, научиться собирать топологию. Изучить построение сети IP-телефонии с помощью маршрутизатора, коммутатора и IP телефонов Cisco 7960 в среде Packet tracer

## Ход выполнения работы

### Часть 1

1. В Cisco Packet Tracer была создана схема соединения: 
![Снимок экрана 2025-04-04 183447](https://github.com/user-attachments/assets/c4b9ee26-1e9d-43ee-a01f-c52880f4b23b)

2. Для каждого ПК прописываем IP-адрес в одной подсети 10.0.0.0/24

3. Выполнены пинги на проверки связности
![Снимок экрана 2025-04-04 184610](https://github.com/user-attachments/assets/682d36ad-88cc-4702-95c3-0aefa569bc9f)

![Снимок экрана 2025-04-04 183727](https://github.com/user-attachments/assets/03a25762-e8c9-45f3-a245-b365eb4372bf)


### Часть 2

1. Размещаем маршрутизатор, коммутатор и два IP-телефона, подключаем их
![Снимок экрана 2025-04-04 190509](https://github.com/user-attachments/assets/a37b677a-d51a-4196-9a3f-d5eaeb8a674d)


2. На роутере назначаем интерфейсу Fa0/0 ip-адрес 10.0.0.1 и включаем его
![Снимок экрана 2025-04-04 185258](https://github.com/user-attachments/assets/d2cf8cab-75ca-4b79-bdf2-abc75ea32cfe)


3. Далее пропишем dhcp pool адресов для телефонов
```
Router(config-if)#ip dhcp pool VoIP
Router(dhcp-config)#network 10.0.0.0 255.255.255.0
Router(dhcp-config)#default-router 10.0.0.1
Router(dhcp-config)#option 150 ip 10.0.0.1
Router(dhcp-config)#exit
```
4. Теперь настроим работу телефонного сервиса. Указываем максимальное количество телефонов и линий, интерфейс и порт, присвоение линий в автоматическом режиме
```
Router(config)#telephony-service
Router(config-telephony)#max-ephones 5
Router(config-telephony)#max-dn 5
Router(config-telephony)#ip source-address 10.0.0.1 port 2000
Router(config-telephony)#auto assign 1 to 5
Router(config-telephony)#exit
```

5. Присвоим номера телефонам
```
Router(config)#ephone-dn 1
Router(config-ephone-dn)#%LINK-3-UPDOWN: Interface ephone_dsp DN 1.1, changed state to up

Router(config-ephone-dn)#number 101
Router(config-ephone-dn)#exit
Router(config)#ephone-dn 2
Router(config-ephone-dn)#%LINK-3-UPDOWN: Interface ephone_dsp DN 2.1, changed state to up

Router(config-ephone-dn)#number 102
Router(config-ephone-dn)#exit
Router(config)#
```

6. Далее на коммутаторе нужно выбрать интерфейсы, к которым подключены роутер и телефоны, перевести их в режим access и указать голосовой vlan
```
Switch(config)#int range Fa0/1-3
Switch(config-if-range)#switchport mode access
Switch(config-if-range)#switchport voice vlan 1
```

7. Включаем телефоны, ждем, пока они получат свои номера, и можно звонить
![Снимок экрана 2025-04-04 190409](https://github.com/user-attachments/assets/5e7b9cd5-991d-4728-824f-9411b48f0d57)
![Снимок экрана 2025-04-04 190420](https://github.com/user-attachments/assets/f4461f89-1e14-468a-8c8d-0fedd55ec134)

## Выводы
В ходе выполнения практического задания была настроена работа простой схемы подключения с L2-коммутаторами и работа IP-телефонии. 
