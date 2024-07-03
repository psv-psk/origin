**Лабраторная работа - Настройка DHCPv6** 

# **Топология**
![This topology has 2 PCs, 2 routers and 2 switches. PC-A is connected to S1 F6. S1 F0/5 is connected to R1 g0/0/1. R1 G0/0/0 is connected to R2 g0/0/0. R2 G0/0/1 is connected to S2 F0/5. S2 F0/18 is connected to PC-B. The use of switches of this lab is optional.](001.png)
# **Таблица адресации**

|**Устройство**|**Интерфейс**|**IPv6-адрес**|
| :-: | :-: | :-: |
|R1|G0/0/0|2001:db8:acad:2::1/64|
|*R1*|*G0/0/0*|fe80::1|
|*R1*|G0/0/1|2001:db8:acad:1::1/64|
|*R1*|*G0/0/1*|fe80::1|
|R2|G0/0/0|2001:db8:acad:2::2/64|
|*R2*|*G0/0*|fe80::2|
|*R2*|G0/0/1|2001:db8:acad:3::1/64|
|*R2*|*G0/0/1*|fe80::1|
|PC-A|NIC|DHCP|
|PC-B|NIC|DHCP|
# **Задачи**
**Часть 1. Создание сети и настройка основных параметров устройства**

**Часть 2. Проверка назначения адреса SLAAC от R1**

**Часть 3. Настройка и проверка сервера DHCPv6 без гражданства на R1**

**Часть 4. Настройка и проверка состояния DHCPv6 сервера на R1**

**Часть 5. Настройка и проверка DHCPv6 Relay на R2**

1. ### **Настройте базовые параметры каждого коммутатора. (необязательно)**

Произведена базовая настройка коммутатора

1. ### **Произведите базовую настройку маршрутизаторов.**
  
Произведена базовая настройка маршрутизатора

\+ 
```
R1(config)#ipv6 unicast-routing

R2(config)#ipv6 unicast-routing
```
1. ### **Настройка интерфейсов и маршрутизации для обоих маршрутизаторов.**
   1. Настройте интерфейсы G0/0/0 и G0/1 на R1 и R2 с адресами IPv6, указанными в таблице выше.
```
R1(config)#interface g0/0/0

R1(config-if)#ipv6 address 2001:db8:acad:2::1/64

R1(config-if)#ipv6 address fe80::1 link-local 

R1(config-if)#exit

R1(config)#interface g0/0/1

R1(config-if)#ipv6 address 2001:db8:acad:1::1/64

R1(config-if)#ipv6 address fe80::1 link-local

R1(config-if)#exit

R2(config)#interface g0/0/0

R2(config-if)#ipv6 address 2001:db8:acad:2::2/64

R2(config-if)#ipv6 address fe80::2 link-local

R2(config-if)#exit

R2(config)#interface g 0/0/1

R2(config-if)#ipv6 address 2001:db8:acad:3::1/64

R2(config-if)#ipv6 address fe80::1 link-local 

R2(config-if)#exit
```
1. Настройте маршрут по умолчанию на каждом маршрутизаторе, который указывает на IP-адрес G0/0/0 на другом маршрутизаторе.
```
   R1(config)#ipv6 route 2001:db8:acad:3::/64 g0/0/0 fe80::2

   R2(config)#ipv6 route 2001:db8:acad:1::/64 g0/0/0 fe80::1
```
1. Убедитесь, что маршрутизация работает с помощью пинга адреса G0/0/1 R2 из R1
```
R1#ping 2001:db8:acad:3::1

Type escape sequence to abort.

Sending 5, 100-byte ICMP Echos to 2001:db8:acad:3::1, timeout is 2 seconds:

!!!!!

Success rate is 100 percent (5/5), round-trip min/avg/max = 0/1/5 ms
```
1. Сохраните текущую конфигурацию в файл загрузочной конфигурации.
```
R1#copy running-config startup-config

Destination filename [startup-config]? 

Building configuration...

[OK]

R1#
```
1. ## **Проверка назначения адреса SLAAC от R1**
```
C:\>ipconfig

FastEthernet0 Connection:(default port)

Connection-specific DNS Suffix..: stateless.com stateful.com 

Link-local IPv6 Address.........: FE80::260:70FF:FE13:595E

IPv6 Address....................: 2001:DB8:ACAD:1:260:70FF:FE13:595E

IPv4 Address....................: 0.0.0.0

Subnet Mask.....................: 0.0.0.0

Default Gateway.................: FE80::1

0\.0.0.0

Bluetooth Connection:

Connection-specific DNS Suffix..: stateless.com stateful.com 

Link-local IPv6 Address.........: ::

IPv6 Address....................: ::

IPv4 Address....................: 0.0.0.0

Subnet Mask.....................: 0.0.0.0

Default Gateway.................: ::

0\.0.0.0
```
1. ## **Настройка и проверка сервера DHCPv6 на R1**
В части 3 выполняется настройка и проверка состояния DHCP-сервера на R1. Цель состоит в том, чтобы предоставить PC-A информацию о DNS-сервере и домене.
1. ### **Более подробно изучите конфигурацию PC-A.**
   1. Выполните команду **ipconfig /all** на PC-A и посмотрите на результат.
```
      C:\>ipconfig /all

      FastEthernet0 Connection:(default port)

      Connection-specific DNS Suffix..: stateless.com stateful.com 

      Physical Address................: 0060.7013.595E

      Link-local IPv6 Address.........: FE80::260:70FF:FE13:595E

      IPv6 Address....................: 2001:DB8:ACAD:1:260:70FF:FE13:595E

      IPv4 Address....................: 0.0.0.0

      Subnet Mask.....................: 0.0.0.0

      Default Gateway.................: FE80::1

      0\.0.0.0

      DHCP Servers....................: 0.0.0.0

      DHCPv6 IAID.....................: 619816607

      DHCPv6 Client DUID..............: 00-01-00-01-E6-D7-B6-8B-00-60-70-13-59-5E

      DNS Servers.....................: 2001:DB8:ACAD::254

      0\.0.0.0
```
1. ### **Настройте R1 для предоставления DHCPv6 без состояния для PC-A.**
   1. Создайте пул DHCP IPv6 на R1 с именем R1-STATELESS. В составе этого пула назначьте адрес DNS-сервера как 2001:db8:acad: :1, а имя домена — как stateless.com.
```
R1(config)# ipv6 dhcp pool R1-STATELESS

R1(config-dhcp)# dns-server 2001:db8:acad::254

R1(config-dhcp)# domain-name STATELESS.com
```
1. Настройте интерфейс G0/0/1 на R1, чтобы предоставить флаг конфигурации OTHER для локальной сети R1 и укажите только что созданный пул DHCP в качестве ресурса DHCP для этого интерфейса.
```
   R1(config)# interface g0/0/1

   R1(config-if)# ipv6 nd other-config-flag 

   R1(config-if)# ipv6 dhcp server R1-STATELESS
```
1. Сохраните текущую конфигурацию в файл загрузочной конфигурации.
1. Перезапустите PC-A.
1. Проверьте вывод **ipconfig /all** и обратите внимание на изменения.
```
   C:\>ipconfig /all

   FastEthernet0 Connection:(default port)

   Connection-specific DNS Suffix..: stateless.com stateful.com 

   Physical Address................: 0060.7013.595E

   Link-local IPv6 Address.........: FE80::260:70FF:FE13:595E

   IPv6 Address....................: 2001:DB8:ACAD:1:260:70FF:FE13:595E

   IPv4 Address....................: 0.0.0.0

   Subnet Mask.....................: 0.0.0.0

   Default Gateway.................: FE80::1

   0\.0.0.0

   DHCP Servers....................: 0.0.0.0

   DHCPv6 IAID.....................: 619816607

   DHCPv6 Client DUID..............: 00-01-00-01-E6-D7-B6-8B-00-60-70-13-59-5E

   DNS Servers.....................: 2001:DB8:ACAD::254
```
1. Тестирование подключения с помощью пинга IP-адреса интерфейса G0/1 R2.
```
   C:\>ping 2001:db8:acad:3::1

   Pinging 2001:db8:acad:3::1 with 32 bytes of data:

   Reply from 2001:DB8:ACAD:3::1: bytes=32 time<1ms TTL=254

   Reply from 2001:DB8:ACAD:3::1: bytes=32 time<1ms TTL=254

   Reply from 2001:DB8:ACAD:3::1: bytes=32 time=6ms TTL=254

   Reply from 2001:DB8:ACAD:3::1: bytes=32 time=1ms TTL=254

   Ping statistics for 2001:DB8:ACAD:3::1:

   Packets: Sent = 4, Received = 4, Lost = 0 (0% loss),

   Approximate round trip times in milli-seconds:

   Minimum = 0ms, Maximum = 6ms, Average = 1ms
```
1. ## **Настройка сервера DHCPv6 с сохранением состояния на R1**
В части 4 настраивается R1 для ответа на запросы DHCPv6 от локальной сети на R2.

1. Создайте пул DHCPv6 на R1 для сети 2001:db8:acad:3:aaa::/80. Это предоставит адреса локальной сети, подключенной к интерфейсу G0/0/1 на R2. В составе пула задайте DNS-сервер 2001:db8:acad: :254 и задайте доменное имя STATEFUL.com.
```
R1(config)# ipv6 dhcp pool R2-STATEFUL

R1(config-dhcp)# address prefix 2001:db8:acad:3:aaa::/80

R1(config-dhcp)# dns-server 2001:db8:acad::254

R1(config-dhcp)# domain-name STATEFUL.com
```
1. Назначьте только что созданный пул DHCPv6 интерфейсу g0/0/0 на R1.
```
   R1(config)# interface g0/0/0

   R1(config-if)# ipv6 dhcp server R2-STATEFUL
```
1. ## **Настройка и проверка ретрансляции DHCPv6 на R2.**
В части 5 необходимо настроить и проверить ретрансляцию DHCPv6 на R2, позволяя PC-B получать адрес IPv6.
1. ### **Включите PC-B и проверьте адрес SLAAC, который он генерирует.**
```
   C:\>ipconfig /all

   FastEthernet0 Connection:(default port)

   Connection-specific DNS Suffix..: 

   Physical Address................: 0010.113A.02A4

   Link-local IPv6 Address.........: FE80::210:11FF:FE3A:2A4

   IPv6 Address....................: ::

   IPv4 Address....................: 0.0.0.0

   Subnet Mask.....................: 0.0.0.0

   Default Gateway.................: ::

   0\.0.0.0

   DHCP Servers....................: 0.0.0.0

   DHCPv6 IAID.....................: 

   DHCPv6 Client DUID..............: 00-01-00-01-98-98-6E-23-00-10-11-3A-02-A4

   DNS Servers.....................: ::

0\.0.0.0
```
1. ### **Настройте R2 в качестве агента DHCP-ретрансляции для локальной сети на G0/0/1.**
   1. Настройте команду **ipv6 dhcp relay** на интерфейсе R2 G0/0/1, указав адрес назначения интерфейса G0/0/0 на R1. Также настройте команду **managed-config-flag** .

```
R2(config)#interface g0/0/1

R2(config-if)#ipv6 nd managed-config-flag

R2(config-if)#ipv6 dhcp relay destination 2001:db8:acad:2::1 g0/0/0

^

% Invalid input detected at '^' marker.
```
1. ### **Попытка получить адрес IPv6 из DHCPv6 на PC-B.**
   1. Перезапустите PC-B.
   1. Откройте командную строку на PC-B и выполните команду **ipconfig /all** и проверьте выходные данные, чтобы увидеть результаты операции ретрансляции DHCPv6.
```
      C:\>ipconfig /all

      FastEthernet0 Connection:(default port)

      Connection-specific DNS Suffix..: 

      Physical Address................: 0010.113A.02A4

      Link-local IPv6 Address.........: FE80::210:11FF:FE3A:2A4

      IPv6 Address....................: ::

      IPv4 Address....................: 0.0.0.0

      Subnet Mask.....................: 0.0.0.0

      Default Gateway.................: ::

      0\.0.0.0

      DHCP Servers....................: 0.0.0.0

      DHCPv6 IAID.....................: 

      DHCPv6 Client DUID..............: 00-01-00-01-98-98-6E-23-00-10-11-3A-02-A4

      DNS Servers.....................: ::

      0\.0.0.0
```
   1. Проверьте подключение с помощью пинга IP-адреса интерфейса R0 G0/0/1.
```
      C:\>ping 2001:db8:acad:1::1

      Pinging 2001:db8:acad:1::1 with 32 bytes of data:

      Request timed out.

      Request timed out.
```
