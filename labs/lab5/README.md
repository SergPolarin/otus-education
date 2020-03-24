## Лабораторная работа. Настройка базового протокола OSPFv2 для одной области

### Топология

![](topology.png)

### Таблица адресации

|Устройство	|Интерфейс   |IP-адрес	     |Маска подсети  |Шлюз по умолчанию |
|:----------|:-----------|:--------------|:--------------|:-----------------|
| R1        |G0/0   	   |192.168.1.1    |255.255.255.0  | -                |
|	          |S0/0/0 (DCE)|192.168.12.1   |255.255.255.252| -                |
|	          |S0/0/1      |192.168.13.1   |255.255.255.252| -                |
| R2        |G0/0 	     |192.168.2.1    |255.255.255.0  | -                |
|           |S0/0/0      |192.168.12.2   |255.255.255.252| -                |
|  	        |S0/0/1 (DCE)|192.168.23.1   |255.255.255.252| -                |
| R3        |G0/0   	   |192.168.3.1    |255.255.255.0  | -                |
|	          |S0/0/0 (DCE)|192.168.13.2   |255.255.255.252| -                |
|	          |S0/0/1 (DCE)|192.168.23.2   |255.255.255.252| -                |
| PC-A      |NIC         |192.168.1.3    |255.255.255.0  | 192.168.1.1      |
| PC-B      |NIC         |192.168.2.3    |255.255.255.0  | 192.168.2.1      |
| PC-C      |NIC         |192.168.3.3    |255.255.255.0  | 192.168.3.1      |


### Задачи

1. Создание сети и настройка основных параметров устройства
2. Настройка и проверка маршрутизации OSPF
3. Изменение назначений идентификаторов маршрутизаторов
4. Настройка пассивных интерфейсов OSPF
5. Изменение метрик OSPF

### Часть 1:	Создание сети и настройка основных параметров устройства

#### Шаг 5:	Проверьте связь.

Эхо-запросы между ПК и шлюзами по-умоланию, а также между маршрутизаторами отправляются успешно

### Часть 2:	Настройка и проверка маршрутизации OSPF

#### Шаг 1:	Настройте протокол OSPF на маршрутизаторе R1.

Команды

    R1(config)route ospf 1
    R1(config-router)# network 192.168.1.0 0.0.0.255 area 0
    R1(config-router)# network 192.168.12.0 0.0.0.3 area 0
    R1(config-router)# network 192.168.13.0 0.0.0.3 area 0
  
    
#### Шаг 2:	Настройте OSPF на маршрутизаторах R2 и R3.

    ...
    по аналогии для остальных маршрутизаторов

#### Шаг 3:	Проверьте информацию о соседних устройствах и маршрутизации OSPF.

Команда

    show ip ospf neighbor
    
Вывод

    R1#show ip ospf neighbor 
    Neighbor ID     Pri   State           Dead Time   Address         Interface
    192.168.23.1      0   FULL/  -        00:00:35    192.168.12.2    Serial0/0/0
    192.168.23.2      0   FULL/  -        00:00:35    192.168.13.2    Serial0/0/1

    R2#sh ip ospf neighbor 
    Neighbor ID     Pri   State           Dead Time   Address         Interface
    192.168.23.2      0   FULL/  -        00:00:31    192.168.23.2    Serial0/0/1
    192.168.13.1      0   FULL/  -        00:00:30    192.168.12.1    Serial0/0/0
    
    R3#sh ip ospf neighbor 
    Neighbor ID     Pri   State           Dead Time   Address         Interface
    192.168.13.1      0   FULL/  -        00:00:36    192.168.13.1    Serial0/0/0
    192.168.23.1      0   FULL/  -        00:00:31    192.168.23.1    Serial0/0/1
    
Команда
    
    show ip route
    
 Вывод
 
    R1#
    ...
         192.168.1.0/24 is variably subnetted, 2 subnets, 2 masks
    C       192.168.1.0/24 is directly connected, GigabitEthernet0/0
    L       192.168.1.1/32 is directly connected, GigabitEthernet0/0
    O    192.168.2.0/24 [110/65] via 192.168.12.2, 00:09:16, Serial0/0/0
    O    192.168.3.0/24 [110/65] via 192.168.13.2, 00:08:01, Serial0/0/1
         192.168.12.0/24 is variably subnetted, 2 subnets, 2 masks
    C       192.168.12.0/30 is directly connected, Serial0/0/0
    L       192.168.12.1/32 is directly connected, Serial0/0/0
         192.168.13.0/24 is variably subnetted, 2 subnets, 2 masks
    C       192.168.13.0/30 is directly connected, Serial0/0/1
    L       192.168.13.1/32 is directly connected, Serial0/0/1
         192.168.23.0/30 is subnetted, 1 subnets
    O       192.168.23.0/30 [110/128] via 192.168.12.2, 00:08:01, Serial0/0/0
                            [110/128] via 192.168.13.2, 00:08:01, Serial0/0/1
    
    R2# 
    ...
    O    192.168.1.0/24 [110/65] via 192.168.12.1, 00:12:18, Serial0/0/0
     192.168.2.0/24 is variably subnetted, 2 subnets, 2 masks
    C       192.168.2.0/24 is directly connected, GigabitEthernet0/0
    L       192.168.2.1/32 is directly connected, GigabitEthernet0/0
    O    192.168.3.0/24 [110/65] via 192.168.23.2, 00:11:04, Serial0/0/1
         192.168.12.0/24 is variably subnetted, 2 subnets, 2 masks
    C       192.168.12.0/30 is directly connected, Serial0/0/0
    L       192.168.12.2/32 is directly connected, Serial0/0/0
         192.168.13.0/30 is subnetted, 1 subnets
    O       192.168.13.0/30 [110/128] via 192.168.12.1, 00:11:04, Serial0/0/0
                            [110/128] via 192.168.23.2, 00:11:04, Serial0/0/1
         192.168.23.0/24 is variably subnetted, 2 subnets, 2 masks
    C       192.168.23.0/30 is directly connected, Serial0/0/1
    L       192.168.23.1/32 is directly connected, Serial0/0/1
    
    R3#
    ...
    O    192.168.1.0/24 [110/65] via 192.168.13.1, 00:11:37, Serial0/0/0
    O    192.168.2.0/24 [110/65] via 192.168.23.1, 00:11:37, Serial0/0/1
         192.168.3.0/24 is variably subnetted, 2 subnets, 2 masks
    C       192.168.3.0/24 is directly connected, GigabitEthernet0/0
    L       192.168.3.1/32 is directly connected, GigabitEthernet0/0
         192.168.12.0/30 is subnetted, 1 subnets
    O       192.168.12.0/30 [110/128] via 192.168.23.1, 00:11:37, Serial0/0/1
                            [110/128] via 192.168.13.1, 00:11:37, Serial0/0/0
         192.168.13.0/24 is variably subnetted, 2 subnets, 2 masks
    C       192.168.13.0/30 is directly connected, Serial0/0/0
    L       192.168.13.2/32 is directly connected, Serial0/0/0
         192.168.23.0/24 is variably subnetted, 2 subnets, 2 masks
    C       192.168.23.0/30 is directly connected, Serial0/0/1
    L       192.168.23.2/32 is directly connected, Serial0/0/1
    
Какую команду вы бы применили, чтобы просмотреть в таблице маршрутизации только маршруты OSPF?

Команда 
    show ip route | include via
    
Вывод

    R1#sh ip route | i via
    O    192.168.2.0/24 [110/65] via 192.168.12.2, 00:14:14, Serial0/0/0
    O    192.168.3.0/24 [110/65] via 192.168.13.2, 00:12:59, Serial0/0/1
    O       192.168.23.0/30 [110/128] via 192.168.12.2, 00:12:59, Serial0/0/0
                            [110/128] via 192.168.13.2, 00:12:59, Serial0/0/1
                           
#### Шаг 4:	Проверьте параметры протокола OSPF.

