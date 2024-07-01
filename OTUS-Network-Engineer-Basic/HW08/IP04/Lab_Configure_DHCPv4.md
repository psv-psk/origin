**Лабораторная работа - Реализация DHCPv4** 

# **Топология**
![This topology has 2 PCs, 2 routers and 2 switches. PC-A is connected to S1 F6. S1 F0/5 is connected to R1 g0/0/1. R1 G0/0/0 is connected to R2 g0/0/0. R2 G0/0/1 is connected to S2 F0/5. S2 F0/18 is connected to PC-B](001.png)
1. # **Таблица адресации**

|**Устройство**|**Интерфейс**|**IP-адрес**|**Маска подсети**|**Шлюз по умолчанию**|
| :-: | :-: | :-: | :-: | :-: |
|R1|G0/0/0|10\.0.0.1|255\.255.255.252|—|
|*R1*|G0/0/1|—|—|*—*|
|*R1*|G0/0/1.100|||*—*|
|*R1*|G0/0/1.200|||*—*|
|*R1*|G0/0/1.1000|—|—|*—*|
|R2|G0/0|10\.0.0.2|255\.255.255.252|—|
|*R2*|G0/0/1|||*—*|
|S1|VLAN 200||||
|S2|VLAN 1||||
|PC-A|NIC|DHCP|DHCP|DHCP|
|PC-B|NIC|DHCP|DHCP|DHCP|
1. # **Таблица VLAN**

|**VLAN**|**Имя**|**Назначенный интерфейс**|
| :-: | :-: | :-: |
|1|Нет|S2: F0/18|
|100|Клиенты|S1: F0/6 |
|200|Управление|S1: VLAN 200  |
|999|Parking\_Lot|S1: F0/1-4, F0/7-24, G0/1-2|
|1000|Собственная|—|
1. # **Задачи**
**Часть 1. Создание сети и настройка основных параметров устройства**

**Часть 2. Настройка и проверка двух серверов DHCPv4 на R1**

**Часть 3. Настройка и проверка DHCP-ретрансляции на R2**
# **Инструкции**
1. ## **Создание сети и настройка основных параметров устройства**
В первой части лабораторной работы вам предстоит создать топологию сети и настроить базовые параметры для узлов ПК и коммутаторов.
1. ### **Создание схемы адресации**

   1\. Для подсети А с 58 хостами:

- Нам необходимо хотя бы 6 бит для разделения на 64 ip (2^6 - 2 = 62 хоста). Таким образом, мы можем использовать 6 бит для хостов, что соответствует маске подсети /26.
- Биты для подсети: 6
- Биты для хостов: 2 (т.к. 8 - 6 = 2)
- Диапазон IP-адресов: 192.168.1.0 - 192.168.1.63

   |**Устройство**|**Интерфейс**|**IP-адрес**|**Маска подсети**|**Шлюз по умолчанию**|
   | :- | :- | :- | :- | :- |
   |R1|G0/0/1.100|192\.168.1.1|255\.255.255.192|-|


   2\. Для подсети Б с 28 хостами:

   - Нам также потребуется 5 бит для разделения на 32 ip (2^5 – 2 = 30 хостов), соответственно маска /27.
   - Биты для подсети: 5 
   - Биты для хостов: 3 (т.к. 8 - 5 = 3)
   - Следующий диапазон IP-адресов: 192.168.1.64 - 192.168.1.95

   |**Устройство**|**Интерфейс**|**IP-адрес**|**Маска подсети**|**Шлюз по умолчанию**|
   | :- | :- | :- | :- | :- |
   |R1|G0/0/1.200|192\.168.1.65|255\.255.255.224|-|
   |S1|VLAN 200|192\.168.1.66|255\.255.255.224|192\.168.1.65|


   3\. Для подсети С с 12 хостами:

   - Для размещения 12 хостов нам потребуется 4 бита (2^4 - 2 = 14 хостов), маска /28.
   - Биты для подсети: 4 
   - Биты для хостов: 4 (т.к. 8 - 4 = 4)
   - Следующий диапазон IP-адресов: 192.168.1.96 - 192.168.1.111

|**Устройство**|**Интерфейс**|**IP-адрес**|**Маска подсети**|**Шлюз по умолчанию**|
| :- | :- | :- | :- | :- |
|R2|G0/0/1|192\.168.1.97|255\.255.255.240||

1. ### **Создайте сеть согласно топологии.**
Подключите устройства, как показано в топологии, и подсоедините необходимые кабели.
1. ### **Произведите базовую настройку маршрутизаторов.**
Для удобства и скорости базовую настройку загружаю из файла ранее настроенных устройств.
1. ### **Настройка маршрутизации между сетями VLAN на маршрутизаторе R1**
```
   R1>en

   Password: 

   R1#conf t

   Enter configuration commands, one per line.  End with CNTL/Z.

   R1(config)#int g0/0/1

   R1(config-if)#no sh

   R1(config-if)#int g0/0/1.100

   R1(config-subif)#

   %LINK-5-CHANGED: Interface GigabitEthernet0/0/1.100, changed state to up

   %LINEPROTO-5-UPDOWN: Line protocol on Interface GigabitEthernet0/0/1.100, changed state to up

   R1(config-subif)#encapsulation dot1Q 100

   R1(config-subif)#ip address 192.168.1.1 255.255.255.192

   R1(config-subif)#description Clients

   R1(config-subif)#

   R1(config-subif)#

   R1(config-subif)#int g0/0/1.200

   R1(config-subif)#

   %LINK-5-CHANGED: Interface GigabitEthernet0/0/1.200, changed state to up

   %LINEPROTO-5-UPDOWN: Line protocol on Interface GigabitEthernet0/0/1.200, changed state to up

   R1(config-subif)#encapsulation dot1Q 200

   R1(config-subif)#ip address 192.168.1.65 255.255.255.224

   R1(config-subif)#description Managment

   R1(config-subif)#int g0/0/1.1000

   R1(config-subif)#

   %LINK-5-CHANGED: Interface GigabitEthernet0/0/1.1000, changed state to up

   %LINEPROTO-5-UPDOWN: Line protocol on Interface GigabitEthernet0/0/1.1000, changed state to up

   R1(config-subif)#encapsulation dot1Q 1000 native 

   R1(config-subif)#description my\_vlan

   R1(config-subif)#
```
Убедитесь, что вспомогательные интерфейсы работают.
```
R1#sh int g0/0/1.100

GigabitEthernet0/0/1.100 is up, line protocol is up (connected)

`  `Hardware is PQUICC\_FEC, address is 0001.9794.6e02 (bia 0001.9794.6e02)

`  `Internet address is 192.168.1.1/26

`  `MTU 1500 bytes, BW 100000 Kbit, DLY 100 usec, 

`     `reliability 255/255, txload 1/255, rxload 1/255

`  `Encapsulation 802.1Q Virtual LAN, Vlan ID 100

`  `ARP type: ARPA, ARP Timeout 04:00:00, 

`  `Last clearing of "show interface" counters never

R1#sh int g0/0/1.200

GigabitEthernet0/0/1.200 is up, line protocol is up (connected)

`  `Hardware is PQUICC\_FEC, address is 0001.9794.6e02 (bia 0001.9794.6e02)

`  `Internet address is 192.168.1.65/27

`  `MTU 1500 bytes, BW 100000 Kbit, DLY 100 usec, 

`     `reliability 255/255, txload 1/255, rxload 1/255

`  `Encapsulation 802.1Q Virtual LAN, Vlan ID 200

`  `ARP type: ARPA, ARP Timeout 04:00:00, 

`  `Last clearing of "show interface" counters never

R1#sh int g0/0/1.1000

GigabitEthernet0/0/1.1000 is up, line protocol is up (connected)

`  `Hardware is PQUICC\_FEC, address is 0001.9794.6e02 (bia 0001.9794.6e02)

`  `MTU 1500 bytes, BW 100000 Kbit, DLY 100 usec, 

`     `reliability 255/255, txload 1/255, rxload 1/255

`  `Encapsulation 802.1Q Virtual LAN, Vlan ID 1000

`  `ARP type: ARPA, ARP Timeout 04:00:00, 

`  `Last clearing of "show interface" counters never
```
1. ### **Настройте G0/1 на R2, затем G0/0/0 и статическую маршрутизацию для обоих маршрутизаторов**
   1. Настройте G0/0/1 на R2 с первым IP-адресом подсети C, рассчитанным ранее.
```
R2(config)#int g0/0/1

R2(config-if)#ip address 192.168.1.97 255.255.255.240
```
1. Настройте интерфейс G0/0/0 для каждого маршрутизатора на основе приведенной выше таблицы IP-адресации.
```
R1(config)#int g0/0/0

R1(config-if)#ip address 10.0.0.1 255.255.255.252



R2(config)#int g0/0/0

R2(config-if)#ip address 10.0.0.2 255.255.255.252
```
1. Настройте маршрут по умолчанию на каждом маршрутизаторе, указываемом на IP-адрес G0/0/0 на другом маршрутизаторе.
```
R1(config)#ip route 0.0.0.0 0.0.0.0 10.0.0.2

R2(config)#ip route 0.0.0.0 0.0.0.0 10.0.0.1
```
1. Убедитесь, что статическая маршрутизация работает с помощью пинга до адреса G0/0/1 R2 от R1.
```
R1#ping 192.168.1.97

Type escape sequence to abort.

Sending 5, 100-byte ICMP Echos to 192.168.1.97, timeout is 2 seconds:

.!!!!

Success rate is 80 percent (4/5), round-trip min/avg/max = 0/0/1 ms
```
1. Сохраните текущую конфигурацию в файл загрузочной конфигурации.
```
R1#copy running-config startup-config

Destination filename [startup-config]? 

Building configuration...

[OK]

R1#
```
1. ### **Настройте базовые параметры каждого коммутатора.**
Для удобства и скорости базовую настройку загружаю из файла ранее настроенных устройств.
1. ### **Создайте сети VLAN на коммутаторе S1.**
Примечание. S2 настроен только с базовыми настройками. 

1. Создайте необходимые VLAN на коммутаторе 1 и присвойте им имена из приведенной выше таблицы.
```
S1>en

Password: 

S1#conf t

Enter configuration commands, one per line. End with CNTL/Z.

S1(config)#vlan 100

S1(config-vlan)#name Clients

S1(config-vlan)#vlan 200

S1(config-vlan)#name Managment

S1(config-vlan)#vlan 999

S1(config-vlan)#name Parking

S1(config-vlan)#vlan 1000

S1(config-vlan)#name my\_vlan

S1(config-vlan)#
```
1. Настройте и активируйте интерфейс управления на S1 (VLAN 200), используя второй IP-адрес из подсети, рассчитанный ранее. Кроме того установите шлюз по умолчанию на S1.
```
S1(config)#int vlan 200

S1(config-if)#

%LINK-5-CHANGED: Interface Vlan200, changed state to up

S1(config-if)#ip address 192.168.1.66 255.255.255.224

S1(config)#ip default-gateway 192.168.1.65
```
1. Настройте и активируйте интерфейс управления на S2 (VLAN 1), используя второй IP-адрес из подсети, рассчитанный ранее. Кроме того, установите шлюз по умолчанию на S2
```
S2(config)#int vlan 1

S2(config-if)#no shutdown 

S2(config-if)#ip address 192.168.1.98 255.255.255.240

S2(config)#ip default-gateway 192.168.1.97
```
1. Назначьте все неиспользуемые порты S1 VLAN Parking\_Lot, настройте их для статического режима доступа и административно деактивируйте их. На S2 административно деактивируйте все неиспользуемые порты.
```
S1(config)#interface range f0/1-4, fa0/7-24, g0/1-2

S1(config-if-range)#switchport mode access 

S1(config-if-range)#switchport access vlan 999

S1(config-if-range)#shutdown 
```
1. ### **Назначьте сети VLAN соответствующим интерфейсам коммутатора.**
   1. Назначьте используемые порты соответствующей VLAN (указанной в таблице VLAN выше) и настройте их для режима статического доступа.
```
S1(config)#int fa0/6

S1(config-if)#switchport access vlan 100

S1(config)#int fa0/6

S1(config-if)# switchport access vlan 100

S2(config)#int f0/18

S2(config-if)#switchport mode access

S2(config-if)#switchport access vlan 1
```

#### Вопрос:
Почему интерфейс F0/5 указан в VLAN 1 - По дефолту все порты в 1-ом vlan
1. ### **Вручную настройте интерфейс S1 F0/5 в качестве транка 802.1Q.**
   1. Измените режим порта коммутатора, чтобы принудительно создать магистральный канал.
```
S1(config)#int f0/5

S1(config-if)#switchport mode trunk 
```
1. В рамках конфигурации транка  установите для native  VLAN значение 1000.
```
S1(config-if)#switchport trunk native vlan 1000
```
1. В качестве другой части конфигурации магистрали укажите, что VLAN 100, 200 и 1000 могут проходить по транку.
```
S1(config-if)#switchport trunk allowed vlan 100,200,1000
```
1. Сохраните текущую конфигурацию в файл загрузочной конфигурации.
```
S1#copy running-config startup-config
```
1. Проверьте состояние транка.
```
S1#sh int f0/5 switchport 

Name: Fa0/5

Switchport: Enabled

Administrative Mode: trunk

Operational Mode: trunk

Administrative Trunking Encapsulation: dot1q

Operational Trunking Encapsulation: dot1q

Negotiation of Trunking: On

Access Mode VLAN: 1 (default)

Trunking Native Mode VLAN: 1000 (my\_vlan)

Voice VLAN: none

Administrative private-vlan host-association: none

Administrative private-vlan mapping: none

Administrative private-vlan trunk native VLAN: none

Administrative private-vlan trunk encapsulation: dot1q

Administrative private-vlan trunk normal VLANs: none

Administrative private-vlan trunk private VLANs: none

Operational private-vlan: none

Trunking VLANs Enabled: 100,200,1000

Pruning VLANs Enabled: 2-1001

Capture Mode Disabled

Capture VLANs Allowed: ALL

Protected: false

Unknown unicast blocked: disabled

Unknown multicast blocked: disabled

Appliance trust: none
```
#### Вопрос:
Какой IP-адрес был бы у ПК, если бы он был подключен к сети с помощью DHCP? - DHCPv4 не настроен, если только стаческий.

1. ## **Настройка и проверка двух серверов DHCPv4 на R1**
В части 2 необходимо настроить и проверить сервер DHCPv4 на R1. Сервер DHCPv4 будет обслуживать две подсети, подсеть A и подсеть C.
1. ### **Настройте R1 с пулами DHCPv4 для двух поддерживаемых подсетей. Ниже приведен только пул DHCP для подсети A**
   1. Исключите первые пять используемых адресов из каждого пула адресов.
```
R1(config)#ip dhcp excluded-address 192.168.1.1 192.168.1.5

R1(config)#ip dhcp excluded-address 192.168.1.97 192.168.1.101
```

1. Создайте пул DHCP (используйте уникальное имя для каждого пула).
```
R1(config)#ip dhcp pool Subnet\_A
```
1. Укажите сеть, поддерживающую этот DHCP-сервер.
```
R1(dhcp-config)#network 192.168.1.0 255.255.255.192
```
1. В качестве имени домена укажите CCNA-lab.com.
```
R1(dhcp-config)#domain-name CCNA-lab.com
```
1. Настройте соответствующий шлюз по умолчанию для каждого пула DHCP.
```
R1(dhcp-config)#default-router 192.168.1.1
```
1. Настройте время аренды на 2 дня 12 часов и 30 минут.
```
   R1(dhcp-config)#lease  - не работает
```
1. Затем настройте второй пул DHCPv4, используя имя пула R2\_Client\_LAN и вычислите сеть, маршрутизатор по умолчанию, и используйте то же имя домена и время аренды, что и предыдущий пул DHCP.
```
R1(config)#ip dhcp pool R2\_Client\_LAN

R1(dhcp-config)#network 192.168.1.96 255.255.255.240

R1(dhcp-config)#domain-name CCNA-lab.com

R1(dhcp-config)#default-router 192.168.1.97
```
1. ### **Сохраните конфигурацию.**
Сохраните текущую конфигурацию в файл загрузочной конфигурации.
```
R1#copy running-config startup-config
```
1. ### **Проверка конфигурации сервера DHCPv4**
   1. Чтобы просмотреть сведения о пуле, выполните команду **show ip dhcp pool** .
```
R1#show ip dhcp pool

Pool Subnet\_A :

Utilization mark (high/low) : 100 / 0

Subnet size (first/next) : 0 / 0 

Total addresses : 62

Leased addresses : 0

Excluded addresses : 2

Pending event : none

1 subnet is currently in the pool

Current index IP address range Leased/Excluded/Total

192\.168.1.1 192.168.1.1 - 192.168.1.62 0 / 2 / 62

Pool R2\_Client\_LAN :

Utilization mark (high/low) : 100 / 0

Subnet size (first/next) : 0 / 0 

Total addresses : 14

Leased addresses : 0

Excluded addresses : 2

Pending event : none

1 subnet is currently in the pool

Current index IP address range Leased/Excluded/Total

192\.168.1.97 192.168.1.97 - 192.168.1.110 0 / 2 / 14

R1#
```
1. Выполните команду **show ip dhcp bindings** для проверки установленных назначений адресов DHCP.
```
R1#show ip dhcp binding

IP address Client-ID/ Lease expiration Type

Hardware address

R1#
```
1. Выполните команду **show ip dhcp server statistics** для проверки сообщений DHCP.
```
R1#show ip dhcp server statistics

^

% Invalid input detected at '^' marker.
```
1. ### **Попытка получить IP-адрес от DHCP на PC-A**
   1. Из командной строки компьютера PC-A выполните команду **ipconfig /all**.
```
C:\>ipconfig /all

FastEthernet0 Connection:(default port)

Connection-specific DNS Suffix..: CCNA-lab.com

Physical Address................: 0001.9633.AAC1

Link-local IPv6 Address.........: FE80::201:96FF:FE33:AAC1

IPv6 Address....................: ::

IPv4 Address....................: 192.168.1.6

Subnet Mask.....................: 255.255.255.192

Default Gateway.................: ::

192\.168.1.1

DHCP Servers....................: 192.168.1.1

DHCPv6 IAID.....................: 

DHCPv6 Client DUID..............: 00-01-00-01-30-9D-01-40-00-01-96-33-AA-C1

DNS Servers.....................: ::

0\.0.0.0

Bluetooth Connection:

Connection-specific DNS Suffix..: CCNA-lab.com

Physical Address................: 0002.17B2.7190

Link-local IPv6 Address.........: ::

IPv6 Address....................: ::

IPv4 Address....................: 0.0.0.0

Subnet Mask.....................: 0.0.0.0

Default Gateway.................: ::

0\.0.0.0

DHCP Servers....................: 0.0.0.0

DHCPv6 IAID.....................: 

DHCPv6 Client DUID..............: 00-01-00-01-30-9D-01-40-00-01-96-33-AA-C1

DNS Servers.....................: ::

0\.0.0.0
```
1. После завершения процесса обновления выполните команду **ipconfig** для просмотра новой информации об IP-адресе.
```
C:\>ipconfig

FastEthernet0 Connection:(default port)

Connection-specific DNS Suffix..: CCNA-lab.com

Link-local IPv6 Address.........: FE80::201:96FF:FE33:AAC1

IPv6 Address....................: ::

IPv4 Address....................: 192.168.1.6

Subnet Mask.....................: 255.255.255.192

Default Gateway.................: ::

192\.168.1.1

Bluetooth Connection:

Connection-specific DNS Suffix..: CCNA-lab.com

Link-local IPv6 Address.........: ::

IPv6 Address....................: ::

IPv4 Address....................: 0.0.0.0

Subnet Mask.....................: 0.0.0.0

Default Gateway.................: ::

0\.0.0.0
```
1. Проверьте подключение с помощью пинга IP-адреса интерфейса R0 G0/0/1.
```
C:\>ping 192.168.1.1

Pinging 192.168.1.1 with 32 bytes of data:

Reply from 192.168.1.1: bytes=32 time<1ms TTL=255

Reply from 192.168.1.1: bytes=32 time<1ms TTL=255

Reply from 192.168.1.1: bytes=32 time<1ms TTL=255

Reply from 192.168.1.1: bytes=32 time<1ms TTL=255

Ping statistics for 192.168.1.1:

Packets: Sent = 4, Received = 4, Lost = 0 (0% loss),

Approximate round trip times in milli-seconds:

Minimum = 0ms, Maximum = 0ms, Average = 0ms
```
1. ## **Настройка и проверка DHCP-ретрансляции на R2**
В части 3 настраивается R2 для ретрансляции DHCP-запросов из локальной сети на интерфейсе G0/0/1 на DHCP-сервер (R1). 
1. ### **Настройка R2 в качестве агента DHCP-ретрансляции для локальной сети на G0/0/1**
   1. Настройте команду **ip helper-address** на G0/0/1, указав IP-адрес G0/0/0 R1.
```
R2(config)#int g0/0/1

R2(config-if)#ip helper-address 10.0.0.1

R2(config-if)#copy running-config startup-config
```
1. ### **Попытка получить IP-адрес от DHCP на PC-B**
   1. Из командной строки компьютера PC-B выполните команду **ipconfig /all**.
```
C:\> ipconfig /all

FastEthernet0 Connection:(default port)

Connection-specific DNS Suffix..: CCNA-lab.com

Physical Address................: 00E0.B0C8.A112

Link-local IPv6 Address.........: FE80::2E0:B0FF:FEC8:A112

IPv6 Address....................: ::

IPv4 Address....................: 192.168.1.102

Subnet Mask.....................: 255.255.255.240

Default Gateway.................: ::

192\.168.1.97

DHCP Servers....................: 10.0.0.1

DHCPv6 IAID.....................: 

DHCPv6 Client DUID..............: 00-01-00-01-BC-CA-A2-4D-00-E0-B0-C8-A1-12

DNS Servers.....................: ::

0\.0.0.0

Bluetooth Connection:

Connection-specific DNS Suffix..: CCNA-lab.com

Physical Address................: 0005.5EEC.D721

Link-local IPv6 Address.........: ::

IPv6 Address....................: ::

IPv4 Address....................: 0.0.0.0

Subnet Mask.....................: 0.0.0.0

Default Gateway.................: ::

0\.0.0.0

DHCP Servers....................: 0.0.0.0

DHCPv6 IAID.....................: 

DHCPv6 Client DUID..............: 00-01-00-01-BC-CA-A2-4D-00-E0-B0-C8-A1-12

DNS Servers.....................: ::

0\.0.0.0
```
1. После завершения процесса обновления выполните команду **ipconfig** для просмотра новой информации об IP-адресе.
```
C:\> ipconfig

FastEthernet0 Connection:(default port)

Connection-specific DNS Suffix..: CCNA-lab.com

Link-local IPv6 Address.........: FE80::2E0:B0FF:FEC8:A112

IPv6 Address....................: ::

IPv4 Address....................: 192.168.1.102

Subnet Mask.....................: 255.255.255.240

Default Gateway.................: ::

192\.168.1.97

Bluetooth Connection:

Connection-specific DNS Suffix..: CCNA-lab.com

Link-local IPv6 Address.........: ::

IPv6 Address....................: ::

IPv4 Address....................: 0.0.0.0

Subnet Mask.....................: 0.0.0.0

Default Gateway.................: ::

0\.0.0.0
```
1. Проверьте подключение с помощью пинга IP-адреса интерфейса R1 G0/0/1.
```
C:\>ping 192.168.1.1

Pinging 192.168.1.1 with 32 bytes of data:

Reply from 192.168.1.1: bytes=32 time<1ms TTL=254

Reply from 192.168.1.1: bytes=32 time<1ms TTL=254

Reply from 192.168.1.1: bytes=32 time<1ms TTL=254

Reply from 192.168.1.1: bytes=32 time<1ms TTL=254

Ping statistics for 192.168.1.1:

Packets: Sent = 4, Received = 4, Lost = 0 (0% loss),

Approximate round trip times in milli-seconds:

Minimum = 0ms, Maximum = 0ms, Average = 0ms
```
1. Выполните **show ip dhcp binding** для R1 для проверки назначений адресов в DHCP.
```
R1#show ip dhcp binding 

IP address Client-ID/ Lease expiration Type

Hardware address

192\.168.1.6 0001.9633.AAC1 -- Automatic

192\.168.1.102 00E0.B0C8.A112 -- Automatic

R1#
```
1. Выполните команду **show ip dhcp server statistics** для проверки сообщений DHCP.
```
R1#show ip dhcp server statistics 

^

% Invalid input detected at '^' marker.
```
