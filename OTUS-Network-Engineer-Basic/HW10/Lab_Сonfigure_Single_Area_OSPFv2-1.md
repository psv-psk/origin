**Лабораторная работа. Настройка протокола OSPFv2 для одной области**

**Лабораторная работа. Настройка протокола OSPFv2 для одной области**

# **Топология**
![This topology has 2 routers and 2 switches. Router R1 has loopback 1 interface and R1 G0/0/1 is connected to S1 F0/5. Switch S1 F0/1 to Switch S2 F0/1. Switch S2 F0/5 is connected to R2 G0/0/1. R2 has loopback 1 interface.](Aspose.Words.69fc12da-974f-437e-849b-570469d64c83.001.png)
# **Таблица адресации**

|**Устройство**|**Интерфейс**|**IP-адрес**|**Маска подсети** |
| :-: | :-: | :-: | :-: |
|R1|G0/0/1|10\.53.0.1|255\.255.255.0|
|*R1*|Loopback1|172\.16.1.1|255\.255.255.0|
|R2|G0/0/1|10\.53.0.2|255\.255.255.0|
|*R2*|Loopback1|192\.168.1.1|255\.255.255.0|
# **Цели**
**Часть 1. Создание сети и настройка основных параметров устройства**

**Часть 2. Настройка и проверка базовой работы протокола  OSPFv2 для одной области**

**Часть 3. Оптимизация и проверка конфигурации OSPFv2 для одной области**
# **Общие сведения и сценарий**
Вам было поручено настроить сеть небольшой компании с помощью OSPFv2. R1 будет размещать интернет-соединение (имитируемое интерфейсом Loopback 1) и делиться информацией о маршруте по умолчанию до  R2. После первоначальной настройки организация попросила оптимизировать конфигурацию, чтобы уменьшить трафик протокола и гарантировать, что R1 продолжает контролировать маршрутизацию.

**Примечание.** Статическая маршрутизация, используемая в данной лаборатории, заключается в оценке возможности настройки и настройки OSPFv2 в конфигурации для одной области. Этот подход, используемый в данной лаборатории, может не отражать рекомендации по работе с сетевыми сетями. 

**Примечание**: Маршрутизаторы, используемые в практических лабораторных работах CCNA, - это Cisco 4221 с Cisco IOS XE Release 16.9.4 (образ universalk9). В лабораторных работах используются коммутаторы Cisco Catalyst 2960 с Cisco IOS версии 15.2(2) (образ lanbasek9). Можно использовать другие маршрутизаторы, коммутаторы и версии Cisco IOS. В зависимости от модели устройства и версии Cisco IOS доступные команды и результаты их выполнения могут отличаться от тех, которые показаны в лабораторных работах. Правильные идентификаторы интерфейса см. в сводной таблице по интерфейсам маршрутизаторов в конце лабораторной работы.

**Примечание.** Убедитесь, что у всех маршрутизаторов и коммутаторов была удалена начальная конфигурация. Если вы не уверены в этом, обратитесь к инструктору.
# **Необходимые ресурсы**
- 2 маршрутизатора (Cisco 4221 с универсальным образом Cisco IOS XE версии 16.9.4 или аналогичным)
- 2 коммутатора (Cisco 2960 с операционной системой Cisco IOS 15.2(2) (образ lanbasek9) или аналогичная модель)
- 1 ПК (под управлением Windows с программой эмуляции терминала, например, Tera Term)
- Консольные кабели для настройки устройств Cisco IOS через консольные порты.
- Кабели Ethernet, расположенные в соответствии с топологией
  # **Инструкции**
  1. ## **Создание сети и настройка основных параметров устройства**
     1. ### **Создайте сеть согласно топологии.**
Подключите устройства, как показано в топологии, и подсоедините необходимые кабели.
1. ### **Произведите базовую настройку маршрутизаторов.**
   *Откройте окно конфигурации*

   1. Загружены базовые конфигурации.
1. ### **Настройте базовые параметры каждого коммутатора.**
   1. Загружены базовые конфигурации.

*Закройте окно настройки.*
1. ## **Настройка и проверка базовой работы протокола OSPFv2 для одной области**
   1. ### **Настройте адреса интерфейса и базового OSPFv2 на каждом маршрутизаторе.**
      1. Настройте адреса интерфейсов на каждом маршрутизаторе, как показано в таблице адресации выше.

R1#conf t

Enter configuration commands, one per line. End with CNTL/Z.

R1(config)#int g0/0/1

R1(config-if)#ip address 10.53.0.1 255.255.255.0

R1(config-if)#no shutdown 

R1(config-if)#interface loopback 1

R1(config-if)#

%LINK-5-CHANGED: Interface Loopback1, changed state to up

%LINEPROTO-5-UPDOWN: Line protocol on Interface Loopback1, changed state to up

ip address 172.16.1.1 255.255.255.0

R1(config-if)#

R1(config-if)#

R1(config-if)#no shutdown

R2(config)#in g0/0/1

R2(config-if)#ip address 10.53.0.2 255.255.255.0

R2(config-if)#no shutdown 

R2(config-if)#int loopback 1

R2(config-if)#

%LINK-5-CHANGED: Interface Loopback1, changed state to up

%LINEPROTO-5-UPDOWN: Line protocol on Interface Loopback1, changed state to up

ip address 192.168.1.1 255.255.255.0

R2(config-if)#no shutdown
1. Перейдите в режим конфигурации маршрутизатора OSPF, используя идентификатор процесса 56.

   R1(config)#router ospf 56

   R2(config)#router ospf 56

1. Настройте статический идентификатор маршрутизатора для каждого маршрутизатора (1.1.1.1 для R1, 2.2.2.2 для R2).

R1(config-router)#router-id 1.1.1.1

R2(config-router)#router-id 2.2.2.2

1. Настройте инструкцию сети для сети между R1 и R2, поместив ее в область 0.

   R1(config-router)#network 10.53.0.1 0.0.0.0 area 0

   R2(config-router)#network 10.53.0.2 0.0.0.0 area 0

1. Только на R2 добавьте конфигурацию, необходимую для объявления сети Loopback 1 в область OSPF 0.

   R2(config-router)#network 10.53.0.2 0.0.0.0 area 0

1. Убедитесь, что OSPFv2 работает между маршрутизаторами. Выполните команду, чтобы убедиться, что R1 и R2 сформировали смежность.

R1#sh ip ospf neighbor

`      `Neighbor ID     Pri   State           Dead Time   Address         Interface

`      `2.2.2.2           1   FULL/DR         00:00:35    10.53.0.2       GigabitEthernet0/0/1

` `R2#show ip ospf neighbor 

`      `Neighbor ID     Pri   State           Dead Time   Address         Interface

`      `1.1.1.1           1   FULL/BDR        00:00:31    10.53.0.1       GigabitEthernet0/0/1
#### Вопрос:
Какой маршрутизатор является DR? Какой маршрутизатор является BDR? Каковы критерии отбора?

R1 стал BDR, R2 стал DR т.к. router-id выше у R2.

1. На R1 выполните команду **show ip route ospf**, чтобы убедиться, что сеть R2 Loopback1 присутствует в таблице маршрутизации. Обратите внимание, что поведение OSPF по умолчанию заключается в объявлении интерфейса обратной связи в качестве маршрута узла с использованием 32-битной маски.

R1#show ip route ospf

192.168.1.0/32 is subnetted, 1 subnets

O 192.168.1.1 [110/2] via 10.53.0.2, 00:06:38, GigabitEthernet0/0/1

1. Запустите Ping до  адреса интерфейса R2 Loopback 1 из R1. Выполнение команды ping должно быть успешным.

R1#ping 192.168.1.1

Type escape sequence to abort.

Sending 5, 100-byte ICMP Echos to 192.168.1.1, timeout is 2 seconds:

!!!!!

Success rate is 100 percent (5/5), round-trip min/avg/max = 0/0/1 ms

*Закройте окно настройки.*
1. ## **Оптимизация и проверка конфигурации OSPFv2 для одной области**
   1. ### **Реализация различных оптимизаций на каждом маршрутизаторе.**
      *Откройте окно конфигурации*

      1. На R1 настройте приоритет OSPF интерфейса G0/0/1 на 50, чтобы убедиться, что R1 является назначенным маршрутизатором.

R1(config)#int g0/0/1

R1(config-if)#ip ospf priority 50

1. Настройте таймеры OSPF на G0/0/1 каждого маршрутизатора для таймера приветствия, составляющего 30 секунд.

R1(config-if)#int g0/0/1

R1(config-if)#ip ospf hello-interval 30

1. На R1 настройте статический маршрут по умолчанию, который использует интерфейс Loopback 1 в качестве интерфейса выхода. Затем распространите маршрут по умолчанию в OSPF. Обратите внимание на сообщение консоли после установки маршрута по умолчанию.

   R1(config)#ip route 0.0.0.0 0.0.0.0 loopback 1

   ` `%Default route without gateway, if not a point-to-point interface, may impact performance

1. добавьте конфигурацию, необходимую для OSPF для обработки R2 Loopback 1 как сети точка-точка. Это приводит к тому, что OSPF объявляет Loopback 1 использует маску подсети интерфейса.

   R1(config)#router ospf 56

   ` `R1(config-router)#default-information originate 

   ` `R1(config-router)#end

   ` `R1#sh ip route

   ` `Gateway of last resort is 0.0.0.0 to network 0.0.0.0

   `      `10.0.0.0/8 is variably subnetted, 2 subnets, 2 masks

   ` `C       10.53.0.0/24 is directly connected, GigabitEthernet0/0/1

   ` `L       10.53.0.1/32 is directly connected, GigabitEthernet0/0/1

   `      `172.16.0.0/16 is variably subnetted, 2 subnets, 2 masks

   ` `C       172.16.1.0/24 is directly connected, Loopback1

   ` `L       172.16.1.1/32 is directly connected, Loopback1

   `      `192.168.1.0/32 is subnetted, 1 subnets

   ` `O       192.168.1.1/32 [110/2] via 10.53.0.2, 00:04:28, GigabitEthernet0/0/1

   ` `S\*   0.0.0.0/0 is directly connected, Loopback1

   ` `R2#sh ip route

   ` `Gateway of last resort is 10.53.0.1 to network 0.0.0.0

   `      `10.0.0.0/8 is variably subnetted, 2 subnets, 2 masks

   ` `C       10.53.0.0/24 is directly connected, GigabitEthernet0/0/1

   ` `L       10.53.0.2/32 is directly connected, GigabitEthernet0/0/1

   `      `192.168.1.0/24 is variably subnetted, 2 subnets, 2 masks

   ` `C       192.168.1.0/24 is directly connected, Loopback1

   ` `L       192.168.1.1/32 is directly connected, Loopback1

   ` `O\*E2 0.0.0.0/0 [110/1] via 10.53.0.1, 00:01:32, GigabitEthernet0/0/1

R2#sh ip ospf interface loopback 1

` `Loopback1 is up, line protocol is up

` `Internet address is 192.168.1.1/24, Area 0

` `Process ID 56, Router ID 2.2.2.2, Network Type LOOPBACK, Cost: 1

` `Loopback interface is treated as a stub Host

` `R2(config)#int loopback 1

` `R2(config-if)#ip ospf network point-to-point 

` `R2#sh ip ospf interface loopback 1

` `Loopback1 is up, line protocol is up

` `Internet address is 192.168.1.1/24, Area 0

` `Process ID 56, Router ID 2.2.2.2, Network Type POINT-TO-POINT, Cost: 1

` `Transmit Delay is 1 sec, State POINT-TO-POINT,

` `Timer intervals configured, Hello 10, Dead 40, Wait 40, Retransmit 5

` `Index 2/2, flood queue length 0

` `Next 0x0(0)/0x0(0)

` `Last flood scan length is 1, maximum is 1

` `Last flood scan time is 0 msec, maximum is 0 msec

` `Suppress hello for 0 neighbor(s)


1. Только на R2 добавьте конфигурацию, необходимую для предотвращения отправки объявлений OSPF в сеть Loopback 1.

R2(config)#router ospf 56

R2(config-router)#	

1. Измените базовую пропускную способность для маршрутизаторов. После этой настройки перезапустите OSPF с помощью команды **clear ip ospf process** . Обратите внимание на сообщение консоли после установки новой опорной полосы пропускания.

R1(config)#router ospf 56

R1(config-router)#auto-cost reference-bandwidth 500

% OSPF: Reference bandwidth is changed.

Please ensure reference bandwidth is consistent across all routers.

R2(config)#router ospf 56

R2(config-router)#auto-cost reference-bandwidth 500

% OSPF: Reference bandwidth is changed.

Please ensure reference bandwidth is consistent across all routers.
1. ### **Убедитесь, что оптимизация OSPFv2 реализовалась.**
   1. Выполните команду **show ip ospf interface g0/0/1** на R1 и убедитесь, что приоритет интерфейса установлен равным 50, а временные интервалы — Hello 30, Dead 120, а тип сети по умолчанию — Broadcast

R1#sh ip ospf interface g0/0/1

GigabitEthernet0/0/1 is up, line protocol is up

Internet address is 10.53.0.1/24, Area 0

Process ID 56, Router ID 1.1.1.1, Network Type BROADCAST, Cost: 5

Transmit Delay is 1 sec, State DR, Priority 50

Designated Router (ID) 1.1.1.1, Interface address 10.53.0.1

Backup Designated Router (ID) 2.2.2.2, Interface address 10.53.0.2

Timer intervals configured, Hello 30, Dead 120, Wait 120, Retransmit 5

Hello due in 00:00:29

Index 1/1, flood queue length 0

Next 0x0(0)/0x0(0)

Last flood scan length is 1, maximum is 1

Last flood scan time is 0 msec, maximum is 0 msec

Neighbor Count is 1, Adjacent neighbor count is 1

Adjacent with neighbor 2.2.2.2 (Backup Designated Router)

Suppress hello for 0 neighbor(s)

1. На R1 выполните команду **show ip route ospf**, чтобы убедиться, что сеть R2 Loopback1 присутствует в таблице маршрутизации. Обратите внимание на разницу в метрике между этим выходным и предыдущим выходным. Также обратите внимание, что маска теперь составляет 24 бита, в отличие от 32 битов, ранее объявленных.

R1#sh ip route ospf 

O 192.168.1.0 [110/5] via 10.53.0.2, 00:02:01, GigabitEthernet0/0/1

1. Введите команду **show ip route ospf** на маршрутизаторе R2. Единственная информация о маршруте OSPF должна быть распространяемый по умолчанию маршрут R1.

R2#sh ip route ospf

O\*E2 0.0.0.0/0 [110/1] via 10.53.0.1, 00:06:28, GigabitEthernet0/0/1

1. Запустите Ping до адреса интерфейса R1 Loopback 1 из R2. Выполнение команды ping должно быть успешным.

R2#ping 172.16.1.1

Type escape sequence to abort.

Sending 5, 100-byte ICMP Echos to 172.16.1.1, timeout is 2 seconds:

!!!!!

Success rate is 100 percent (5/5), round-trip min/avg/max = 0/0/1 ms
#### Вопрос:


Почему стоимость OSPF для маршрута по умолчанию отличается от стоимости OSPF в R1 для сети 192.168.1.0/24?

Стоимость интерфейса Loopback всегда равна 1. Администратор сети может вручную настраивать стоимость OSPF для конкретных интерфейсов или диапазонов адресов, что также может привести к различиям в стоимости для одной и той же сети
© ã 2019 г. - гггг Корпорация Cisco и/или ее дочерние компании. Все права защищены. Открытая информация Cisco 	страница 6** 6**	www.netacad.com
