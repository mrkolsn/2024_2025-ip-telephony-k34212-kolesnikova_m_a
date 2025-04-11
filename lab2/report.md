University: ITMO University

Faculty: PIn

Course: IP-telephony

Year: 2024/2025

Group: K34212

Author: Kolesnikova Maria Alexeevna

Lab: Lab2

Date of create: 04.04.2025

Date of finished: 04.04.2025

# Лабораторная работа №2 "Конфигурация voip в среде Сisco packet tracer"

## Описание
Для выполнения данной лабораторной работы собирается схема соединения. Необходимо проверить, правильно ли подключены все узлы устройств. Предварительно удалить все преды- дущие конфигурационные файлы на маршрутизаторах Cisco 2811, на коммутаторе Cisco catalyst 3560.

## Цель работы
Изучить построение сети IP-телефонии с помощью маршрутизатора Cisco 2811, коммутатора Cisco catalyst 3560 и IP телефонов Cisco 7960.

## Ход выполнения работы

### Часть 1

1. В Cisco Packet Tracer собираем схему соединения, согласно заданию
![Снимок экрана 2025-04-04 190509](https://github.com/user-attachments/assets/8bc96424-31af-4625-b12e-dc8eeea587da)

2. На маршрутизаторе отключаем DNS lookup и задаем пароли для защиты доступа к консоли локально и удаленно

```
CMERouter(config-if)#no ip domain-lookup
CMERouter(config)#enable secret cisco
CMERouter(config)#line console 0
CMERouter(config-line)#password cisco
CMERouter(config-line)#login
CMERouter(config-line)#exit
CMERouter(config)#line vty 0 4
CMERouter(config-line)#password cisco
CMERouter(config-line)#login
CMERouter(config-line)#exit
```

3. На роутере присвоим ip-адрес интерфейсу Fa0/0
![Снимок экрана 2025-04-04 194403](https://github.com/user-attachments/assets/753853a8-b3f5-4225-a8fb-322767c8af33)

4. Настроим DHCP и передачу голоса аналогично прошлой лабораторной работе, присвоим номера телефонам
```
CMERouter(config-if)#ip dhcp pool VoIP
CMERouter(dhcp-config)#network 10.0.0.0 255.255.255.0
CMERouter(dhcp-config)#default-router 10.0.0.1
CMERouter(dhcp-config)#option 150 ip 10.0.0.1
CMERouter(dhcp-config)#exit
CMERouter(config)#telephony-service
CMERouter(config-telephony)#max-ephone 5
CMERouter(config-telephony)#max-dn 5
CMERouter(config-telephony)#ip source-address 10.0.0.1 port 2000
CMERouter(config-telephony)#auto assign 1 to 5
CMERouter(config-telephony)#exit
CMERouter(config)#ephone-dn 1
CMERouter(config-ephone-dn)#%LINK-3-UPDOWN: Interface ephone_dsp DN 1.1, changed state to up

CMERouter(config-ephone-dn)#number 101
CMERouter(config-ephone-dn)#exit
CMERouter(config)#ephone-dn 2
%IPPHONE-6-REGISTER: ephone-1 IP:10.0.0.2 Socket:2 DeviceType:Phone has registered.

CMERouter(config-ephone-dn)#%LINK-3-UPDOWN: Interface ephone_dsp DN 2.1, changed state to up

CMERouter(config-ephone-dn)#number 102
CMERouter(config-ephone-dn)#exit
CMERouter(config)#ephone-dn 3
CMERouter(config-ephone-dn)#%LINK-3-UPDOWN: Interface ephone_dsp DN 3.1, changed state to up

CMERouter(config-ephone-dn)#number
%IPPHONE-6-REGISTER: ephone-2 IP:10.0.0.4 Socket:2 DeviceType:Phone has registered.
 
CMERouter(config-ephone-dn)#number 103
CMERouter(config-ephone-dn)#exit
CMERouter(config)#
%IPPHONE-6-REGISTER: ephone-3 IP:10.0.0.3 Socket:2 DeviceType:Phone has registered.
```

5. На коммутаторе указываем голосовой vlan
```
Switch(config)#int range fa0/1-4
Switch(config-if-range)#switchport voice vlan 1
```

6. Подключаем телефоны и проверяем связность
![Снимок экрана 2025-04-04 200102](https://github.com/user-attachments/assets/8d9ecb0f-7a58-4fc5-9c3a-b1f23ae78b48)


### Часть 2

1. Собираем схему согласно заданию

![Снимок экрана 2025-04-11 112651](https://github.com/user-attachments/assets/06f27d9b-8c0a-4d16-97c1-69d3f51ab89c)
   
2. На коммутаторе настроим vlan 10 и 20 для передачи данных и голоса соответственно
```
Switch(config)#vlan 10
Switch(config-vlan)#name data
Switch(config-vlan)#exit
Switch(config)#vlan 20
Switch(config-vlan)#name voice
Switch(config-vlan)#exit
Switch(config)#int range fa1/1
Switch(config-if-range)#switchport mode access
Switch(config-if-range)#switchport voice vlan 20
Switch(config-if-range)#switchport access vlan 20
Switch(config-if-range)#switchport access vlan 10
Switch(config-if-range)#exit
Switch(config)#int range fa2/1
Switch(config-if-range)#switchport mode access
Switch(config-if-range)#switchport voice vlan 20
Switch(config-if-range)#switchport access vlan 10
Switch(config-if-range)#exit
Switch(config)#int range fa3/1
Switch(config-if-range)#switchport mode access
Switch(config-if-range)#switchport voice vlan 20
Switch(config-if-range)#switchport access vlan 10
Switch(config-if-range)#exit
Switch(config)#
```
3. Теперь настроим роутер. Добавим DHCP пулы:
```
Router(config)#ip dhcp pool VoIP
Router(dhcp-config)#network 10.20.0.0 255.255.255.0
Router(dhcp-config)#default-router 10.20.0.1
Router(dhcp-config)#option 150 ip 10.20.0.1
Router(dhcp-config)#exit
Router(config)#ip dhcp excluded-address 10.20.0.1
Router(config)#ip dhcp excluded-address 10.10.0.1
Router(config)#ip dhcp pool Data
Router(dhcp-config)#network 10.10.0.0 255.255.255.0
Router(dhcp-config)#default-router 10.10.0.1
Router(dhcp-config)#exit
```

Пропишем саб-интерфейсы:
```
Router(config)#int fa0/0.10
Router(config-subif)#encapsulation Dot1Q 10
Router(config-subif)#ip address 10.10.0.1 255.255.255.0
Router(config-subif)#exit
Router(config)#int fa0/0.20
Router(config-subif)#encapsulation Dot1Q 20
Router(config-subif)#ip address 10.20.0.1 255.255.255.0
Router(config-subif)#exit
```

И аналогично предыдущему заданию поднимаем и настраиваем телефонный сервис
```
Router(config)#telephony-service
Router(config-telephony)#max-ephones 5
Router(config-telephony)#max-dn 5
Router(config-telephony)#ip source-address 10.0.0.1 port 2000
Router(config-telephony)#auto assign 1 to 5
Router(config-telephony)#exit
Router(config)#ephone-dn 1
Router(config-ephone-dn)#%LINK-3-UPDOWN: Interface ephone_dsp DN 1.1, changed state to up

Router(config-ephone-dn)#number 101
Router(config-ephone-dn)#exit
Router(config)#ephone-dn 2
Router(config-ephone-dn)#%LINK-3-UPDOWN: Interface ephone_dsp DN 2.1, changed state to up

Router(config-ephone-dn)#number 102
Router(config-ephone-dn)#exit
Router(config)#ephone-dn 3
Router(config-ephone-dn)#%LINK-3-UPDOWN: Interface ephone_dsp DN 3.1, changed state to up

Router(config-ephone-dn)#number 103
Router(config-ephone-dn)#exit
```

4. Теперь нужно подключить телефоны, включить на ПК протокол DHCP и можно выполнять проверку

![Снимок экрана 2025-04-11 112739](https://github.com/user-attachments/assets/5290c98e-9bef-4b93-b077-72a3d3004128)

## Выводы
В ходе выполнения лабораторной работы были настроены две схемы подключения с IP-телефонией
