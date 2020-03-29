## Лабораторная работа. Настройка OSPFv2 для нескольких областей

### Топология

![](topology.png)

### Таблица адресации

|Устройство	|Интерфейс   |IP-адрес	     |Маска подсети  |
|:----------|:-----------|:--------------|:--------------|
| R1        |Lo0   	     |209.165.200.225|255.255.255.252|
|	          |Lo1         |192.168.1.1    |255.255.255.0  |
|	          |Lo2         |192.168.2.1    |255.255.255.0  |
|	          |S0/0/0 (DCE)|192.168.12.1   |255.255.255.252|
| R2        |Lo6  	     |192.168.6.1    |255.255.255.0  |
|           |S0/0/0      |192.168.12.2   |255.255.255.252|
|  	        |S0/0/1 (DCE)|192.168.23.1   |255.255.255.252|
| R3        |Lo4    	   |192.168.4.1    |255.255.255.0  |
|	          |Lo5         |192.168.5.1    |255.255.255.0  |
|	          |S0/0/1      |192.168.23.2   |255.255.255.252|

### Задачи
1. Создание сети и настройка основных параметров устройства
2. Настройка сети OSPFv2 для нескольких областей
3. Настройка межобластных суммарных маршрутов

### Часть 1:	Создание сети и настройка основных параметров устройства

#### Шаг 4:	Проверьте наличие подключения на уровне 3.

### Часть 2:	Настройка сети OSPFv2 для нескольких областей

#### Шаг 1:	Определите типы маршрутизаторов OSPF в топологии.

Определите магистральные маршрутизаторы: Маршрутизаторы R1 и R2 являются магистральными, так как их последовательные интерфейсы
s0/0/0 находятся в зоне 0

Определите граничные маршрутизаторы автономной системы (ASBR): Маршрутизатор R1 является ASBR, так как его интерфейс Lo0 представляет собой выход во внешнюю систему (AS)

Определите граничные маршрутизаторы области (ABR): Маршрутизатор R2 является граничным, потому что соединяет две зоны

Определите внутренние маршрутизаторы: Маршрутизатор R3 является внутренним маршрутизатором, потому что все его интерфейсы находятся в одной зоне 3

#### Шаг 2:	Настройте протокол OSPF на маршрутизаторе R1.

Команды на R1

    conf t
    router ospf 1
    router-id 1.1.1.1
    network 192.168.1.0 0.0.0.255 area 1
    network 192.168.2.0 0.0.0.255 area 1
    network 192.168.12.0 0.0.0.3 area 0
    passive-interface loopback 1
    passive-interface loopback 2
    ip route 0.0.0.0 0.0.0.0 loopback 0 
    default-information originate 

Вывод

    R1#sh ip protocols 
    Routing Protocol is "ospf 1"
      Outgoing update filter list for all interfaces is not set 
      Incoming update filter list for all interfaces is not set 
      Router ID 1.1.1.1
      It is an autonomous system boundary router
      Redistributing External Routes from,
        static 
      Number of areas in this router is 2. 2 normal 0 stub 0 nssa
      Maximum path: 4
      Routing for Networks:
        192.168.1.0 0.0.0.255 area 1
        192.168.2.0 0.0.0.255 area 1
        192.168.12.0 0.0.0.3 area 0
      Passive Interface(s): 
        Loopback1
        Loopback2
      Routing Information Sources:  
        Gateway         Distance      Last Update 
        1.1.1.1              110      00:06:08
        2.2.2.2              110      00:06:08
      Distance: (default is 110)
      
#### Шаг 3:	Настройте протокол OSPF на маршрутизаторе R2.

Команды на R2

    conf t
    router ospf 1
    router-id 2.2.2.2
    network 192.168.6.0 0.0.0.255 area 3
    network 192.168.12.0 0.0.0.3 area 0
    network 192.168.23.0 0.0.0.3 area 3
    passive-interface loopback 6

Вывод

    R2# sh ip protocols 
    Routing Protocol is "ospf 1"
      Outgoing update filter list for all interfaces is not set 
      Incoming update filter list for all interfaces is not set 
      Router ID 2.2.2.2
      Number of areas in this router is 2. 2 normal 0 stub 0 nssa
      Maximum path: 4
      Routing for Networks:
        192.168.6.0 0.0.0.255 area 3
        192.168.23.0 0.0.0.3 area 3
        192.168.12.0 0.0.0.3 area 0
      Passive Interface(s): 
        Loopback6
      Routing Information Sources:  
        Gateway         Distance      Last Update 
        1.1.1.1              110      00:01:15
        2.2.2.2              110      00:01:15
      Distance: (default is 110)

    R2#sh ip route ospf 
         192.168.1.0/32 is subnetted, 1 subnets
    O IA    192.168.1.1 [110/782] via 192.168.12.1, 00:03:28, Serial0/0/0
         192.168.2.0/32 is subnetted, 1 subnets
    O IA    192.168.2.1 [110/782] via 192.168.12.1, 00:03:28, Serial0/0/0

    R2#sh ip ospf neighbor 
    Neighbor ID     Pri   State           Dead Time   Address         Interface
    1.1.1.1           0   FULL/  -        00:00:30    192.168.12.1    Serial0/0/0
    
    
#### Шаг 4:	Настройте протокол OSPF на маршрутизаторе R3.

Команды на R3

    conf t
    router ospf 1
    router-id 3.3.3.3
    network 192.168.4.0 0.0.0.255 area 3
    network 192.168.5.0 0.0.0.255 area 3
    network 192.168.23.0 0.0.0.3 area 3
    passive-interface loopback 4
    passive-interface loopback 5
    
Вывод 

    R3#sh ip protocols 
    Routing Protocol is "ospf 1"
      Outgoing update filter list for all interfaces is not set 
      Incoming update filter list for all interfaces is not set 
      Router ID 3.3.3.3
      Number of areas in this router is 1. 1 normal 0 stub 0 nssa
      Maximum path: 4
      Routing for Networks:
        192.168.4.0 0.0.0.255 area 3
        192.168.5.0 0.0.0.255 area 3
        192.168.23.0 0.0.0.3 area 3
      Passive Interface(s): 
        Loopback4
        Loopback5
      Routing Information Sources:  
        Gateway         Distance      Last Update 
        2.2.2.2              110      00:01:47
        3.3.3.3              110      00:01:47
      Distance: (default is 110)
      
    R3#sh ip route ospf 
         192.168.1.0/32 is subnetted, 1 subnets
    O IA    192.168.1.1 [110/1563] via 192.168.23.1, 00:02:15, Serial0/0/1
         192.168.2.0/32 is subnetted, 1 subnets
    O IA    192.168.2.1 [110/1563] via 192.168.23.1, 00:02:15, Serial0/0/1
         192.168.6.0/32 is subnetted, 1 subnets
    O       192.168.6.1 [110/782] via 192.168.23.1, 00:02:15, Serial0/0/1
         192.168.12.0/30 is subnetted, 1 subnets
    O IA    192.168.12.0 [110/1562] via 192.168.23.1, 00:02:15, Serial0/0/1
    
    R3#sh ip ospf neighbor 
    Neighbor ID     Pri   State           Dead Time   Address         Interface
    2.2.2.2           0   FULL/  -        00:00:30    192.168.23.1    Serial0/0/1

#### Шаг 5:	Убедитесь в правильности настройки протокола OSPF и в установлении отношений смежности между маршрутизаторами.

К какому типу маршрутизаторов OSPF относится каждый маршрутизатор?
R1: ASBR
R2: ABR
R3: внутренний маршрутизатор

#### Шаг 6:	Настройте аутентификацию MD5 для всех последовательных интерфейсов.

Команды

    conf t
    int s0/0/0
    ip ospf authentication message-digest
    ip ospf authentication-key Cisco123
    
Вывод на R2

    interface Serial0/0/0
     bandwidth 128
     ip address 192.168.12.2 255.255.255.252
     ip ospf authentication message-digest
     ip ospf authentication-key 7 0802455D0A16544541
    !
    interface Serial0/0/1
     bandwidth 128
     ip address 192.168.23.1 255.255.255.252
     ip ospf authentication message-digest
     ip ospf authentication-key 7 0802455D0A16544541
     clock rate 128000
 
Почему перед настройкой аутентификации OSPF полезно проверить правильность работы OSPF?

???

### Часть 3:	Настройка межобластных суммарных маршрутов

    R1#sh ip route ospf 
         192.168.4.0/32 is subnetted, 1 subnets
    O IA    192.168.4.1 [110/1563] via 192.168.12.2, 00:08:48, Serial0/0/0
         192.168.5.0/32 is subnetted, 1 subnets
    O IA    192.168.5.1 [110/1563] via 192.168.12.2, 00:08:48, Serial0/0/0
         192.168.6.0/32 is subnetted, 1 subnets
    O IA    192.168.6.1 [110/782] via 192.168.12.2, 00:11:23, Serial0/0/0
         192.168.23.0/30 is subnetted, 1 subnets
    O IA    192.168.23.0 [110/1562] via 192.168.12.2, 00:11:23, Serial0/0/0

b.	Повторите команду show ip route ospf для R2 и R3. Запишите маршруты OSPF между областями для каждого маршрутизатора.

    R2#sh ip route ospf 
         192.168.1.0/32 is subnetted, 1 subnets
    O IA    192.168.1.1 [110/782] via 192.168.12.1, 00:12:44, Serial0/0/0
         192.168.2.0/32 is subnetted, 1 subnets
    O IA    192.168.2.1 [110/782] via 192.168.12.1, 00:12:44, Serial0/0/0
    
    R3#sh ip route ospf 
     192.168.1.0/32 is subnetted, 1 subnets
    O IA    192.168.1.1 [110/1563] via 192.168.23.1, 00:10:25, Serial0/0/1
         192.168.2.0/32 is subnetted, 1 subnets
    O IA    192.168.2.1 [110/1563] via 192.168.23.1, 00:10:25, Serial0/0/1
         192.168.6.0/32 is subnetted, 1 subnets
    O       192.168.6.1 [110/782] via 192.168.23.1, 00:10:25, Serial0/0/1
         192.168.12.0/30 is subnetted, 1 subnets
    O IA    192.168.12.0 [110/1562] via 192.168.23.1, 00:10:25, Serial0/0/1
    
Шаг 2:	Просмотрите базы данных LSDB на всех маршрутизаторах.

    R1#sh ip ospf database 
                OSPF Router with ID (1.1.1.1) (Process ID 1)
                    Router Link States (Area 0)
    Link ID         ADV Router      Age         Seq#       Checksum Link count
    1.1.1.1         1.1.1.1         964         0x80000005 0x0083a0 2
    2.2.2.2         2.2.2.2         964         0x80000005 0x00d44d 2
                    Summary Net Link States (Area 0)
    Link ID         ADV Router      Age         Seq#       Checksum
    192.168.1.1     1.1.1.1         395         0x80000003 0x00aa42
    192.168.2.1     1.1.1.1         395         0x80000004 0x009d4d
    192.168.4.1     2.2.2.2         810         0x80000005 0x0005ce
    192.168.5.1     2.2.2.2         810         0x80000006 0x00f7d9
    192.168.6.1     2.2.2.2         390         0x80000007 0x004d92
    192.168.23.0    2.2.2.2         390         0x80000008 0x001ba7
                    Router Link States (Area 1)
    Link ID         ADV Router      Age         Seq#       Checksum Link count
    1.1.1.1         1.1.1.1         409         0x80000004 0x0098c4 2
                    Summary Net Link States (Area 1)
    Link ID         ADV Router      Age         Seq#       Checksum
    192.168.12.0    1.1.1.1         405         0x8000000e 0x00b810
    192.168.6.1     1.1.1.1         959         0x80000008 0x0007cb
    192.168.23.0    1.1.1.1         959         0x80000009 0x00d4e0
    192.168.4.1     1.1.1.1         805         0x8000000c 0x00b20e
    192.168.5.1     1.1.1.1         805         0x8000000d 0x00a519
                    Summary ASB Link States (Area 1)
    Link ID         ADV Router      Age         Seq#       Checksum
    2.2.2.2         1.1.1.1         959         0x80000007 0x0092a9
                    Type-5 AS External Link States
    Link ID         ADV Router      Age         Seq#       Checksum Tag
    0.0.0.0         1.1.1.1         409         0x80000002 0x00fcd0 1
    
b.	Повторите команду show ip ospf database для R2 и R3. Запишите идентификаторы каналов (Link ID) для состояний суммарных сетевых каналов (Summary Net Link State) каждой области.

    R2#sh ip ospf database 
                OSPF Router with ID (2.2.2.2) (Process ID 1)
                    Summary Net Link States (Area 0)
    Link ID         ADV Router      Age         Seq#       Checksum
    192.168.6.1     2.2.2.2         552         0x80000007 0x004d92
    192.168.23.0    2.2.2.2         552         0x80000008 0x001ba7
    192.168.4.1     2.2.2.2         972         0x80000005 0x0005ce
    192.168.5.1     2.2.2.2         972         0x80000006 0x00f7d9
    192.168.1.1     1.1.1.1         557         0x80000003 0x00aa42
    192.168.2.1     1.1.1.1         557         0x80000004 0x009d4d
                    Summary Net Link States (Area 3)
    Link ID         ADV Router      Age         Seq#       Checksum
    192.168.12.0    2.2.2.2         552         0x80000008 0x009439
    192.168.1.1     2.2.2.2         1121        0x80000006 0x0024b1
    192.168.2.1     2.2.2.2         1121        0x80000007 0x0017bc
                    Summary ASB Link States (Area 3)
    Link ID         ADV Router      Age         Seq#       Checksum
    1.1.1.1         2.2.2.2         1121        0x80000005 0x00a697
    ***************************************************************
    R3#sh ip ospf database 
                OSPF Router with ID (3.3.3.3) (Process ID 1)
                    Summary Net Link States (Area 3)
    Link ID         ADV Router      Age         Seq#       Checksum
    192.168.1.1     2.2.2.2         1133        0x80000006 0x0024b1
    192.168.2.1     2.2.2.2         1133        0x80000007 0x0017bc
    192.168.12.0    2.2.2.2         564         0x80000008 0x009439
                    Summary ASB Link States (Area 3)
    Link ID         ADV Router      Age         Seq#       Checksum
    1.1.1.1         2.2.2.2         1133        0x80000005 0x00a697
    
Шаг 3:	Настройте межобластные суммарные маршруты.

c.	Рассчитайте суммарный маршрут для сетей в области 3. Запишите результаты.

    192.168.4.0 255.255.252.0
    
d.	Настройте суммарный маршрут для области 3 на маршрутизаторе R2. Запишите использованные команды в отведённой ниже области.

Команды

    conf t
    router ospf 1
    area 3 range 192.168.4.0 255.255.252.0
    
#### Шаг 4:	Повторно отобразите таблицы маршрутизации OSPF для всех маршрутизаторов.

Вывод 

    R1#sh ip route ospf 
    O IA 192.168.4.0 [110/782] via 192.168.12.2, 00:01:52, Serial0/0/0
         192.168.23.0/30 is subnetted, 1 subnets
    O IA    192.168.23.0 [110/1562] via 192.168.12.2, 00:56:53, Serial0/0/0
    
    R2#  sh ip route ospf 
    O IA 192.168.0.0 [110/782] via 192.168.12.1, 00:30:01, Serial0/0/0
         192.168.4.0/32 is subnetted, 1 subnets
    O       192.168.4.1 [110/782] via 192.168.23.2, 00:55:01, Serial0/0/1
         192.168.5.0/32 is subnetted, 1 subnets
    O       192.168.5.1 [110/782] via 192.168.23.2, 00:55:01, Serial0/0/1
    O*E2 0.0.0.0/0 [110/1] via 192.168.12.1, 00:57:31, Serial0/0/0

    R3#sh ip route ospf 
    O IA 192.168.0.0 [110/1563] via 192.168.23.1, 00:30:58, Serial0/0/1
         192.168.6.0/32 is subnetted, 1 subnets
    O       192.168.6.1 [110/782] via 192.168.23.1, 00:56:03, Serial0/0/1
         192.168.12.0/30 is subnetted, 1 subnets
    O IA    192.168.12.0 [110/1562] via 192.168.23.1, 00:56:03, Serial0/0/1
    O*E2 0.0.0.0/0 [110/1] via 192.168.23.1, 00:56:03, Serial0/0/1
    
#### Шаг 5:	Просмотрите базы данных LSDB на всех маршрутизаторах.

    R1#sh ip ospf database 
                    Summary Net Link States (Area 0)
    Link ID         ADV Router      Age         Seq#       Checksum
    192.168.0.0     1.1.1.1         128         0x80000015 0x008c53
    192.168.23.0    2.2.2.2         1199        0x8000000c 0x0013ab
    192.168.4.0     2.2.2.2         284         0x8000000d 0x00528d
                    Summary Net Link States (Area 1)
    Link ID         ADV Router      Age         Seq#       Checksum
    192.168.12.0    1.1.1.1         1213        0x80000014 0x00ac16
    192.168.23.0    1.1.1.1         1767        0x80000011 0x00c4e8
    192.168.4.0     1.1.1.1         438         0x80000015 0x00fdcd
                    Summary ASB Link States (Area 1)
    Link ID         ADV Router      Age         Seq#       Checksum
    2.2.2.2         1.1.1.1         1767        0x8000000f 0x0082b1
    
    R2#sh ip ospf database 
                    Summary Net Link States (Area 0)
    Link ID         ADV Router      Age         Seq#       Checksum
    192.168.23.0    2.2.2.2         1248        0x8000000c 0x0013ab
    192.168.4.0     2.2.2.2         334         0x8000000d 0x00528d
    192.168.0.0     1.1.1.1         179         0x80000015 0x008c53
                    Summary Net Link States (Area 3)
    Link ID         ADV Router      Age         Seq#       Checksum
    192.168.12.0    2.2.2.2         1248        0x8000000b 0x008e3c
    192.168.0.0     2.2.2.2         173         0x8000000c 0x001eb6
                    Summary ASB Link States (Area 3)
    Link ID         ADV Router      Age         Seq#       Checksum
    1.1.1.1         2.2.2.2         16          0x8000000d 0x00969f
    
    R3#sh ip os d
                    Summary Net Link States (Area 3)
    Link ID         ADV Router      Age         Seq#       Checksum
    192.168.12.0    2.2.2.2         1284        0x8000000b 0x008e3c
    192.168.0.0     2.2.2.2         209         0x8000000c 0x001eb6
                    Summary ASB Link States (Area 3)
    Link ID         ADV Router      Age         Seq#       Checksum
    1.1.1.1         2.2.2.2         52          0x8000000d 0x00969f

#### Шаг 6:	Проверьте наличие сквозного соединения.

Проверка доступности сетей каждого маршрутизатора прошла успешно
