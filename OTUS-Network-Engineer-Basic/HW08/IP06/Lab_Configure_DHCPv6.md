**Лабраторная работа - Настройка DHCPv6**

**Лабраторная работа - Настройка DHCPv6** 

# **Топология**
![This topology has 2 PCs, 2 routers and 2 switches. PC-A is connected to S1 F6. S1 F0/5 is connected to R1 g0/0/1. R1 G0/0/0 is connected to R2 g0/0/0. R2 G0/0/1 is connected to S2 F0/5. S2 F0/18 is connected to PC-B. The use of switches of this lab is optional.](Aspose.Words.17b062da-8c55-457b-88b1-3afcb7b2e04c.001.png)
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
# **Общие сведения и сценарий**
Динамическое назначение глобальных индивидуальных IPv6-адресов можно настроить тремя способами:

- Автоматическая конфигурация адреса без сохранения состояния (Stateless Address Autoconfiguration, SLAAC)
- DHCPv6 без отслеживания состояния
- Адресация DHCPv6 с учетом состояний.

При использовании SLACC для назначения адресов IPv6 хостам сервер DHCPv6 не используется. Поскольку DHCPv6 сервер не используется при реализации SLACC, хосты не могут получать дополнительную важную сетевую информацию, включая адрес сервера доменных имен (DNS), а также имя домена.

При использовании Stateless DHCPv6 для назначения адресов IPv6 хосту сервер DHCPv6 используется для назначения дополнительной важной информации о сети, однако адрес IPv6 назначается с помощью SLACC.

При использовании DHCPv6 с отслеживанием состояния, сервер DHCP назначает всю информацию, включая IPv6-адрес узла.

Определение способа получения динамической IPv6-адресации зависит от установленных значений флагов, содержащихся в объявлениях маршрутизатора (сообщениях RA).

В предложенном сценарии размеры компании увеличились, и сетевые администраторы больше не имеют возможности назначать IP-адреса для устройств вручную. Ваша задача - настроить маршрутизатор R2 для назначения адресов IPv6 в двух разных подсетях, подключенных к маршрутизатору R1.

**Примечание**: Маршрутизаторы, используемые в практических лабораторных работах CCNA, - это Cisco 4221 с Cisco IOS XE Release 16.9.4 (образ universalk9). В лабораторных работах используются коммутаторы Cisco Catalyst 2960 с Cisco IOS версии 15.2(2) (образ lanbasek9). Можно использовать другие маршрутизаторы, коммутаторы и версии Cisco IOS. В зависимости от модели устройства и версии Cisco IOS доступные команды и результаты их выполнения могут отличаться от тех, которые показаны в лабораторных работах. Правильные идентификаторы интерфейса см. в сводной таблице по интерфейсам маршрутизаторов в конце лабораторной работы.
# **Инструкции**
1. ## **Создание сети и настройка основных параметров устройства**
В первой части лабораторной работы вам предстоит создать топологию сети и настроить базовые параметры для узлов ПК и коммутаторов.
1. ### **Создайте сеть согласно топологии.**
Подключите устройства, как показано в топологии, и подсоедините необходимые кабели.
1. ### **Настройте базовые параметры каждого коммутатора. (необязательно)**
   *Откройте окно конфигурации*

Произведена базовая настройка коммутатора

*Закройте окно настройки.*
1. ### **Произведите базовую настройку маршрутизаторов.**
   *Откройте окно конфигурации*

Произведена базовая настройка маршрутизатора

\+ 

R1(config)#ipv6 unicast-routing

R2(config)#ipv6 unicast-routing
1. ### **Настройка интерфейсов и маршрутизации для обоих маршрутизаторов.**
   1. Настройте интерфейсы G0/0/0 и G0/1 на R1 и R2 с адресами IPv6, указанными в таблице выше.

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

1. Настройте маршрут по умолчанию на каждом маршрутизаторе, который указывает на IP-адрес G0/0/0 на другом маршрутизаторе.

   R1(config)#ipv6 route 2001:db8:acad:3::/64 g0/0/0 fe80::2

   R2(config)#ipv6 route 2001:db8:acad:1::/64 g0/0/0 fe80::1

1. Убедитесь, что маршрутизация работает с помощью пинга адреса G0/0/1 R2 из R1

R1#ping 2001:db8:acad:3::1

Type escape sequence to abort.

Sending 5, 100-byte ICMP Echos to 2001:db8:acad:3::1, timeout is 2 seconds:

!!!!!

Success rate is 100 percent (5/5), round-trip min/avg/max = 0/1/5 ms

1. Сохраните текущую конфигурацию в файл загрузочной конфигурации.

R1#copy running-config startup-config

Destination filename [startup-config]? 

Building configuration...

[OK]

R1#

*Закройте окно настройки.*
1. ## **Проверка назначения адреса SLAAC от R1**
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
1. ## **Настройка и проверка сервера DHCPv6 на R1**
В части 3 выполняется настройка и проверка состояния DHCP-сервера на R1. Цель состоит в том, чтобы предоставить PC-A информацию о DNS-сервере и домене.
1. ### **Более подробно изучите конфигурацию PC-A.**
   1. Выполните команду **ipconfig /all** на PC-A и посмотрите на результат.

      C:\Users\Student> **ipconfig /all**

      Windows IP Configuration

      `   `Host Name . . . . . . . . . . . . : НАСТОЛЬНАЯ 3FR7RKA

      `   `Primary Dns Suffix . . . . . . . : 

      `   `Node Type . . . . . . . . . . . . : Hybrid

      `   `IP Routing Enabled. . . . . . . . : No

      `   `WINS Proxy Enabled. . . . . . . . : No

      Ethernet adapter Ethernet0:

      `   `Connection-specific DNS Suffix . : 

      `   `Description . . . . . . . . . . . : Intel(R) 852574L Gigabit Network Connection 

      `   `Physical Address. . . . . . . . . : 00-50-56-83-63-6D

      `   `IPv6 Address. . . . . . . . . . . : 2001:db8:acad:1:5c43:ee7c:2959:da68(Preferred)

      `   `Temporary IPv6 Address. . . . . . : 2001:db8:acad:1:3c64:e4f9:46e1:1f23(Preferred)

      `   `Link-local IPv6-адрес. . . . . : fe80::5c43:ee7c:2959:da68%6(Preferred)

      `   `IPv4 Address. . . . . . . . . . . : 169.254.218.104(Preferred)

      `   `Subnet Mask . . . . . . . . . . . : 255.255.0.0

      `   `Шлюз по умолчанию . . . . . . . . .: fe80።1%6

      `   `DHCPv6 IAID . . . . . . . . . . . : 50334761

      `   `DHCPv6 Client DUID.  . . . . . . . : 00-01-00-01-24-F5-CE-A2-00-50-56-B3-63-6D

      `   `DNS-серверы . . . . . . . . . . . : fec0:0:0:ffff::1%1

      `                                       `fec0:0:0:ffff::2%1

      `                                       `fec0:0:0:ffff::3%1

      `   `NetBIOS over Tcpip. . . . . . . . : Enabled

   1. Обратите внимание, что основной DNS-суффикс отсутствует. Также обратите внимание, что предоставленные адреса DNS-сервера являются адресами «локального сайта anycast», а не одноадресные адреса, как ожидалось.
1. ### **Настройте R1 для предоставления DHCPv6 без состояния для PC-A.**
   1. Создайте пул DHCP IPv6 на R1 с именем R1-STATELESS. В составе этого пула назначьте адрес DNS-сервера как 2001:db8:acad: :1, а имя домена — как stateless.com.

*Откройте окно конфигурации*

R1(config)# **ipv6 dhcp pool R1-STATELESS**

R1(config-dhcp)# **dns-server 2001:db8:acad::254**

R1(config-dhcp)# **domain-name STATELESS.com**

1. Настройте интерфейс G0/0/1 на R1, чтобы предоставить флаг конфигурации OTHER для локальной сети R1 и укажите только что созданный пул DHCP в качестве ресурса DHCP для этого интерфейса.

   R1(config)# **interface g0/0/1**

   R1(config-if)# **ipv6 nd other-config-flag** 

   R1(config-if)# **ipv6 dhcp server R1-STATELESS**

1. Сохраните текущую конфигурацию в файл загрузочной конфигурации.
1. Перезапустите PC-A.
1. Проверьте вывод **ipconfig /all** и обратите внимание на изменения.

   C:\Users\Student> **ipconfig /all**

   Windows IP Configuration 

   `   `Host Name . . . . . . . . . . . . : DESKTOP-3FR7RKA

   `   `Primary Dns Suffix . . . . . . . : 

   `   `Node Type . . . . . . . . . . . . : Hybrid

   `   `IP Routing Enabled. . . . . . . . : No

   `   `WINS Proxy Enabled. . . . . . . . : No

   `   `DNS Suffix Search List. . . . . . : STATELESS.com

   Ethernet adapter Ethernet0:

   `   `Connection-specific DNS Suffix . : STATELESS.com

   `   `Описание . . . . . . . . . . . : Intel(R) 82574L Gigabit Network Connection

   `   `Physical Address. . . . . . . . . : 00-50-56-83-63-6D

   `   `DHCP Enabled. . . . . . . . . . . : Yes

   `   `Autoconfiguration Enabled . . . . : Yes

   `   `IPv6 Address. . . . . . . . . . . : 2001:db8:acad:1:5c43:ee7c:2959:da68(Preferred)

   `   `Temporary IPv6 Address. . . . . . : 2001:db8:acad:1:3c64:e4f9:46e1:1f23(Preferred)

   `   `Link-local IPv6-адрес. . . . . : fe80::5c43:ee7c:2959:da68%6(Preferred)

   `   `IPv4 Address. . . . . . . . . . . : 169.254.218.104(Preferred)

   `   `Subnet Mask . . . . . . . . . . . : 255.255.0.0

   `   `Default Gateway . . . . . . . . .: fe80።1%6

   `   `DHCPv6 IAID . . . . . . . . . . . : 50334761

   `   `DHCPv6 Client DUID. . . . . . . . : 00-01-00-01-24-F5-CE-A2-00-50-56-B3-63-6D

   `   `DNS Servers . . . . . . . . . . . : 2001:db8:acad። 254

   `   `NetBIOS over Tcpip. . . . . . . . : Enabled

   `   `Список поиска DNS-суффиксов подключения: 

   `                                       `STATELESS.com

1. Тестирование подключения с помощью пинга IP-адреса интерфейса G0/1 R2.
1. ## **Настройка сервера DHCPv6 с сохранением состояния на R1**
В части 4 настраивается R1 для ответа на запросы DHCPv6 от локальной сети на R2.

1. Создайте пул DHCPv6 на R1 для сети 2001:db8:acad:3:aaa::/80. Это предоставит адреса локальной сети, подключенной к интерфейсу G0/0/1 на R2. В составе пула задайте DNS-сервер 2001:db8:acad: :254 и задайте доменное имя STATEFUL.com.

*Откройте окно конфигурации*

R1(config)# **ipv6 dhcp pool R2-STATEFUL**

R1(config-dhcp)# **address prefix 2001:db8:acad:3:aaa::/80**

R1(config-dhcp)# **dns-server 2001:db8:acad::254**

R1(config-dhcp)# **domain-name STATEFUL.com**

1. Назначьте только что созданный пул DHCPv6 интерфейсу g0/0/0 на R1.

   R1(config)# **interface g0/0/0**

   R1(config-if)# **ipv6 dhcp server R2-STATEFUL**

*Закройте окно настройки.*
1. ## **Настройка и проверка ретрансляции DHCPv6 на R2.**
В части 5 необходимо настроить и проверить ретрансляцию DHCPv6 на R2, позволяя PC-B получать адрес IPv6.
1. ### **Включите PC-B и проверьте адрес SLAAC, который он генерирует.**
C:\Users\Student> **ipconfig /all**

Windows IP Configuration

`   `Host Name . . . . . . . . . . . . : DESKTOP-3FR7RKA

`   `Primary Dns Suffix . . . . . . . : 

`   `Node Type . . . . . . . . . . . . : Hybrid

`   `IP Routing Enabled. . . . . . . . : No

`   `WINS Proxy Enabled. . . . . . . . : No

Ethernet adapter Ethernet0:

`   `Connection-specific DNS Suffix . : 

`   `Description . . . . . . . . . . . : Intel(R) 82574L Gigabit Network Connection

`   `Physical Address. . . . . . . . . : 00-50-56-B3-7B-06

`   `DHCP Enabled. . . . . . . . . . . : Yes

`   `Autoconfiguration Enabled . . . . : Yes

`   `IPv6 Address. . . . . . . . . . . : 2001:db8:acad:3:a0f3:3d39:f9fb:a020(Preferred) 

`   `Temporary IPv6 Address. . . . . . : 2001:db8:acad:3:d4f3:7b16:eeee:b2b5(Preferred) 

`   `Link-local IPv6 address. . . . . : fe80::a0f3:3d39:f9fb:a020%6(Preferred) 

`   `IPv4 Address. . . . . . . . . . . : 169.254.160.32(Preferred) 

`   `Subnet Mask . . . . . . . . . . . : 255.255.0.0

`   `Default Gateway . . . . . . . . .: fe80።1%6

`   `DHCPv6 IAID . . . . . . . . . . . : 50334761

`   `DHCPv6 Client DUID. . . . . . . . : 00-01-00-01-24-F2-08-38-00-50-56-B3-7B-06

`   `DNS Servers . . . . . . . . . . . : fec0:0:0:ffff::1%1

`                                       `fec0:0:0:ffff::2%1

`                                       `fec0:0:0:ffff::3%1

`   `NetBIOS over Tcpip. . . . . . . . : Enabled

Обратите внимание на вывод, что используется префикс 2001:db8:acad:3::
1. ### **Настройте R2 в качестве агента DHCP-ретрансляции для локальной сети на G0/0/1.**
   1. Настройте команду **ipv6 dhcp relay** на интерфейсе R2 G0/0/1, указав адрес назначения интерфейса G0/0/0 на R1. Также настройте команду **managed-config-flag** .

*Откройте окно конфигурации*

R2 (конфигурация) # **интерфейс g0/0/1**

R2(config-if)# **ipv6 nd managed-config-flag**

R2(config-if)# **ipv6 dhcp relay destination 2001:db8:acad:2::1 g0/0/0**

1. Сохраните конфигурацию.

*Закройте окно настройки.*
1. ### **Попытка получить адрес IPv6 из DHCPv6 на PC-B.**
   1. Перезапустите PC-B.
   1. Откройте командную строку на PC-B и выполните команду **ipconfig /all** и проверьте выходные данные, чтобы увидеть результаты операции ретрансляции DHCPv6.

      C:\Users\Student> **ipconfig /all**

      Windows IP Configuration

      `   `Host Name . . . . . . . . . . . . : DESKTOP-3FR7RKA

      `   `Primary Dns Suffix . . . . . . . : 

      `   `Node Type . . . . . . . . . . . . : Hybrid

      `   `IP Routing Enabled. . . . . . . . : No

      `   `WINS Proxy Enabled. . . . . . . . : No

      `   `DNS Suffix Search List. . . . . . : STATEFUL.com

      Ethernet adapter Ethernet0:

      `   `Connection-specific DNS Suffix . : STATEFUL.com

      `   `Description . . . . . . . . . . . : Intel(R) 852574L Gigabit Network Connection

      `   `Physical Address. . . . . . . . . : 00-50-56-B3-7B-06

      `   `DHCP Enabled. . . . . . . . . . . : Yes

      `   `Autoconfiguration Enabled . . . . : Yes

      `   `IPv6 Address. . . . . . . . . . . : 2001:db8:acad3:aaaa:7104:8b7d:5402(Preferred)

      `   `Lease Obtained. . . . . . . . . . : Sunday, October 6, 2019 3:27:13 PM

      `   `Lease Expires . . . . . . . . . . Tuesday, October 8, 2019 3:27:13 PM

      `   `Link-local IPv6-адрес. . . . . : fe80::a0f3:3d39:f9fb:a020%6(Preferred)

      `   `IPv4 Address. . . . . . . . . . . : 169.254.160.32(Preferred)

      `   `Subnet Mask . . . . . . . . . . . : 255.255.0.0

      `   `Default Gateway . . . . . . . . .: fe80። 2% 6

      `   `DHCPv6 IAID . . . . . . . . . . . : 50334761

      `   `DHCPv6 Client DUID. . . . . . . . : 00-01-00-01-24-F2-08-38-00-50-56-B3-7B-06

      `   `DNS Servers . . . . . . . . . . . : 2001:db8:acad። 254

      `   `NetBIOS over Tcpip. . . . . . . . : Включен

      `   `Список поиска DNS-суффиксов подключения:

      `                                       `STATEFUL.com

   1. Проверьте подключение с помощью пинга IP-адреса интерфейса R0 G0/0/1.
© ã 2017 г. - гггг Корпорация Cisco и/или ее дочерние компании. Все права защищены. Открытая информация Cisco 	страница 2** 7**	www.netacad.com
