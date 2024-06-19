   ﻿**Лабораторная работа. Базовая настройка коммутатора** 

# **Топология**
![](001.png)
# **Таблица адресации**

|**Устройство**|**Интерфейс**|**IP-адрес / префикс**|
| :-: | :-: | :-: |
|S1|VLAN 1|192\.168.1.2 /24|
|PC-A|NIC|192\.168.1.10 /24|
# **Задачи**
**Часть 1. Проверить конфигурации коммутатора по умолчанию**

**Часть 2. Создать сеть и настроить основные параметры устройств**

- Настройка базовых параметров коммутатора.
- Настройка IP-адреса для ПК.

**Часть 3. Проверка сетевых подключений**

- Отображение конфигурации устройства.
- Тестирование сквозного соединения.
- Тестирование возможности удаленного управления с помощью Telnet.

# **Решение**
1. ## **Создание сети и проверка настроек коммутатора по умолчанию**
  1. ### **Создал сеть согласно топологии.**
Подсоединил консольный кабель, как показано в топологии. На данном этапе кабель Ethernet компьютера PC-A не подключал.
Установил консольное подключение к коммутатору с компьютера PC-A с помощью приложения Terminal.
Для первоначальной настройки коммутатора нужно использовать консольное подключение в связи с тем, что коммутатор не имеет никаких настроек (в том числе IP-адреса) и подключиться по нему удаленно, используя Telnet или SSH подключение невозможно.

  2. ### **Проверка настройки коммутатора по умолчанию.**
   1. Через эмуляцию терминала на PC0 получил доступ к командной строке пользовательского режима (Switch>).
      Для входа в привилегированный режим EXEC ввел команду **enable**,
```
      Switch#
```
С помощью команды **show running-config** привилегированного режима EXEC убедился, что на коммутаторе находится пустой файл конфигурации по умолчанию.

   2. **running configuration(коммутатор 2960)**:

      24 интерфейса FastEthernet
   
      2 интерфейса Gigabit Ethernet
   
      Диапазон vty: 0-4 и 5-15.

4. **startup configuration в ОЗУ (NVRAM)**.
```
Switch# show startup-config
startup-config is not present
```

Текущая конфигурация не сохранялась.

1. <a name="bm3"></a>Характеристики SVI для VLAN 1.

   IP-адрес сети VLAN 1 не назначен (no ip address)

   интерфейс включен (shutdown)

   MAC-адрес SVI: **address is 0010.11b9.67a0** 

1. IP-свойства интерфейса SVI сети VLAN 1.
```
Switch# show ip interface vlan 1
Vlan1 is administratively down, line protocol is down
Internet protocol processing disabled
```
   Интерфейс отключен, обработка IP отключена. 

1. Подсоединил кабель Ethernet компьютера PC-A к порту 6 на коммутаторе 

IP-свойства интерфейса SVI сети VLAN 1. 

Изменений нет.

1. Изучил сведения о версии ОС Cisco IOS на коммутаторе:

   Информация об ОС: 
```
Cisco IOS Software, C2960 Software (C2960-LANBASEK9-M), Version 15.0(2)SE4, RELEASE SOFTWARE (fc1)
  System image file is "flash:c2960-lanbasek9-mz.150-2.SE4.bin"
  Base ethernet MAC Address : 00:10:11:B9:67:A0
```
1. Свойства по умолчанию интерфейса FastEthernet, 
```
   show interface f0/6:
```
   Интерфейс включен (FastEthernet0/6 is up, line protocol is up (connected))

   При физическом подключение Ethernet кабеля интерфейс поднимается:

   FastEthernet0/6 is up, line protocol is up (connected)

   Какой MAC-адрес у интерфейса **: 000c.cf78.a706**

   **Full-duplex, 100Mb/s**

1. Параметры сети VLAN по умолчанию на коммутаторе
```
   Switch# **show vlan** 
   VLAN Name Status Ports 
   1 default active  (Все Fa0/1- Fa0/23; Gig0/1, Gig0/2)
```
   По умолчанию: Native VLAN

2.
```Switch# show flash
   Имя образа Cisco IOS: 2960-lanbasek9-mz.150-2.SE4.bin
```
1. ## **Настройка базовых параметров сетевых устройств**
   1. ### **Настройка базовые параметры коммутатора.**
a.    В режиме глобальной конфигурации задал следующие базовые параметры конфигурации: 
```
         no ip domain-lookup
         hostname S1
         service password-encryption
         enable secret class
         banner motd #
         Unauthorized access is strictly prohibited. #
```

b.    Назначение IP-адрес интерфейсу SVI.

```
         S1(config)# interface vlan 1
         S1(config-if)# ip address 192.168.1.2 255.255.255.0 
         S1(config-if)# no shutdown
```

c.    Ограничение доступа через порт консоли. 

```
         S1(config)# line con 0
         S1(config-line)# logging synchronous 
         S1(config-line)# password cisco
         S1(config-line)# login
```

d.    Настройка vty, для доступа через Telnet. 

```
         S1(config) # line vty 0 4
         S1(config-line)# password cisco
         S1(config-line)# login
```

Login нужен для включения доступа к VTY


1. ### **Настройка IP-адреса на компьютере PC-A.**
В соответствии с таблицей адресации PC-A заданы IP-адрес и маска подсети: 192.168.1.10, маска 255.255.255.0
1. ## **Проверка сетевых подключений**
   1. ### **Отобразите конфигурацию коммутатора.**
Использовал консольное подключение на компьютере PC-A для отображения и проверки конфигурации коммутатора. Ввел команду **show run** для отображения действующей конфигурации:

1. конфигурация приведена ниже. 

```
   S1#sh running-config 
   Building configuration...
   Current configuration : 1293 bytes
   !
   version 15.0

   no service timestamps log datetime msec
   no service timestamps debug datetime msec
   service password-encryption

   !
   hostname S1
   !
   enable secret 5 $1$mERr$9cTjUIEqNGurQiFU.ZeCi1
   !

   !

   !

   no ip domain-lookup

   !
   !
   !

   spanning-tree mode pvst
   spanning-tree extend system-id
   !
   interface FastEthernet0/1
   !
   interface FastEthernet0/2
   !
   interface FastEthernet0/3
   !
   interface FastEthernet0/4
   !
   interface FastEthernet0/5
   !
   interface FastEthernet0/6
   !
   interface FastEthernet0/7
   !
   interface FastEthernet0/8
   !
   interface FastEthernet0/9
   !
   interface FastEthernet0/10
   !
   interface FastEthernet0/11
   !
   interface FastEthernet0/12
   !
   interface FastEthernet0/13
   !
   interface FastEthernet0/14
   !
   interface FastEthernet0/15
   !
   interface FastEthernet0/16
   !
   interface FastEthernet0/17
   !
   interface FastEthernet0/18
   !
   interface FastEthernet0/19
   !
   interface FastEthernet0/20
   !
   interface FastEthernet0/21
   !
   interface FastEthernet0/22
   !
   interface FastEthernet0/23
   !
   interface FastEthernet0/24
   !
   interface GigabitEthernet0/1
   !
   interface GigabitEthernet0/2
   !
   interface Vlan1
   ip address 192.168.1.2 255.255.255.0
   !
   banner motd ^C Unauthorized access is strictly prohibited. ^C
   !
   !
   !
   line con 0
   password 7 0822455D0A16
   logging synchronous
   login
   !
   line vty 0 4
   password 7 0822455D0A16
   login
   line vty 5 15
   login
   !
   !
   !
   !
   end
```

1. Параметры VLAN 1.
```
   S1# show interface vlan 1 
   BW 100000 Kbit
   Vlan1 is up, line protocol is up
```
1. ### **Протестировал сквозное соединение, отправив эхо-запрос.**
   1.
```
      C:\>ping 192.168.1.10 

      Pinging 192.168.1.10 with 32 bytes of data:
      Reply from 192.168.1.10: bytes=32 time=17ms TTL=128
      Reply from 192.168.1.10: bytes=32 time=10ms TTL=128
      Reply from 192.168.1.10: bytes=32 time=10ms TTL=128
      Reply from 192.168.1.10: bytes=32 time=20ms TTL=128
```

   1.
```
     C:\>ping 192.168.1.2

Pinging 192.168.1.2 with 32 bytes of data:
Request timed out.
Reply from 192.168.1.2: bytes=32 time<1ms TTL=255
Reply from 192.168.1.2: bytes=32 time<1ms TTL=255
Reply from 192.168.1.2: bytes=32 time<1ms TTL=255
Ping statistics for 192.168.1.2:

Packets: Sent = 4, Received = 3, Lost = 1 (25% loss)
```

Время ожидания передачи первого пакета истекло в связи с пустой ARP таблицей. Далее пакеты прошли.
1. ### **Удаленное управление коммутатором S1.**
   1. В Telnet/SSH указал адрес S1 192.168.1.2 и подключился к нему. 

```
Trying 192.168.1.2 ...Open Unauthorized access is strictly prohibited. 
User Access Verification
Password: 
S1>en
Password: 
S1#
```
1. Сохранение конфигурации.

**copy running-config startup-config**.
# **Вопросы для повторения**
1. Пароль VTY нужен для ограничения доступа к управлению коммутатором
1. Для шифрования паролей использовать **service password-encryption**
# **Приложение А. Инициализация и перезагрузка коммутатора**
1. Подключился из консоли:

```
   Unauthorized access is strictly prohibited. 
   User Access Verification
   Password: 

   S1>en
   Password: 

   S1#show flash
   Directory of flash:/

   1 -rw- 4670455 <no date> 2960-lanbasek9-mz.150-2.SE4.bin
   2 -rw- 1293 <no date> config.text
   64016384 bytes total (59344636 bytes free)
   S1#
```

1. Файл **vlan.dat** нее, так как VLAN 1 по умолчанию.
1. Удаление загрузочной конфигурации из NVRAM. 

```
   S1#erase startup-config 
   Erasing the nvram filesystem will remove all configuration files! Continue? [confirm]
   [OK]

   Erase of nvram: complete
   %SYS-7-NV\_BLOCK\_INIT: Initialized the geometry of nvram
   S1#
```

1. Перезагрузка S1

```
S1# reload
Proceed with reload? [confirm]
```

1. После перезагрузки коммутатор загрузился с настройками по умолчанию 

```
   Press RETURN to get started!

   %LINK-5-CHANGED: Interface FastEthernet0/6, changed state to up
   %LINEPROTO-5-UPDOWN: Line protocol on Interface FastEthernet0/6, changed state to up

   Switch>
```

   P.S. сообщение о поднятом интерфейсе т.к. Ethernet кабель не отключали

