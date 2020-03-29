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

