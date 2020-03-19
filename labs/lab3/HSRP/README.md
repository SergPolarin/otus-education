## Лабораторная работа. Настройка HSRP

### Топология

![](topology.png)

### Таблица адресации

|Устройство	|Интерфейс   |IP-адрес	     |Маска подсети  |Шлюз по умолчанию |
|:----------|:-----------|:--------------|:--------------|:-----------------|
| R1        |G0/1   	   |192.168.1.1    |255.255.255.0  | -                |
|	          |S0/0/0 (DCE)|10.1.1.1       |255.255.255.252| -                |
| R2        |S0/0/0 	   |10.1.1.2       |255.255.255.252| -                |
|           |S0/0/1 (DCE)|10.2.2.2       |255.255.255.252| -                |
|  	        |Lo1         |209.165.200.225|255.255.255.224| -                |
| R3        |G0/1   	   |192.168.1.3    |255.255.255.0  | -                |
|	          |S0/0/1      |10.2.2.1       |255.255.255.252| -                |
| S1        |VLAN 1      |192.168.1.11   |255.255.255.0  | 192.168.1.1      |
| S3        |VLAN 1      |192.168.1.13   |255.255.255.0  | 192.168.1.3      |
| PC-A      |NIC         |192.168.1.31   |255.255.255.0  | 192.168.1.1      |
| PC-C      |NIC         |192.168.1.33   |255.255.255.0  | 192.168.1.3      |


### Задачи

1. Построение сети и проверка соединения
2. Настройка обеспечения избыточности на первом хопе с помощью HSRP

### Часть 1:	Построение сети и проверка связи

Отправьте ping-запрос с компьютера PC-A на компьютер PC-C. Удалось ли получить ответ? ДА

Вывод команды show ip route на всех маршрутизаторах

R1

    Gateway of last resort is 10.1.1.2 to network 0.0.0.0

         10.0.0.0/8 is variably subnetted, 4 subnets, 3 masks
    R       10.0.0.0/8 is possibly down, routing via 10.1.1.2, Serial0/0/0
    C       10.1.1.0/30 is directly connected, Serial0/0/0
    L       10.1.1.1/32 is directly connected, Serial0/0/0
    R       10.2.2.0/30 [120/1] via 10.1.1.2, 00:00:24, Serial0/0/0
         192.168.1.0/24 is variably subnetted, 2 subnets, 2 masks
    C       192.168.1.0/24 is directly connected, GigabitEthernet0/1
    L       192.168.1.1/32 is directly connected, GigabitEthernet0/1
    R*   0.0.0.0/0 [120/1] via 10.1.1.2, 00:00:24, Serial0/0/0
    
R2

    Gateway of last resort is not set

         10.0.0.0/8 is variably subnetted, 5 subnets, 3 masks
    R       10.0.0.0/8 is possibly down, routing via 10.2.2.1, Serial0/0/1
    C       10.1.1.0/30 is directly connected, Serial0/0/0
    L       10.1.1.2/32 is directly connected, Serial0/0/0
    C       10.2.2.0/30 is directly connected, Serial0/0/1
    L       10.2.2.2/32 is directly connected, Serial0/0/1
    R    192.168.1.0/24 [120/1] via 10.1.1.1, 00:00:08, Serial0/0/0
                        [120/1] via 10.2.2.1, 00:00:12, Serial0/0/1
         209.165.200.0/24 is variably subnetted, 3 subnets, 3 masks
    S       209.165.200.0/24 [1/0] via 209.165.200.224
    C       209.165.200.224/27 is directly connected, Loopback0
    L       209.165.200.225/32 is directly connected, Loopback0

R3

    Gateway of last resort is 10.2.2.2 to network 0.0.0.0

         10.0.0.0/8 is variably subnetted, 4 subnets, 3 masks
    R       10.0.0.0/8 is possibly down, routing via 192.168.1.1, GigabitEthernet0/1
    R       10.1.1.0/30 [120/1] via 10.2.2.2, 00:00:04, Serial0/0/1
    C       10.2.2.0/30 is directly connected, Serial0/0/1
    L       10.2.2.1/32 is directly connected, Serial0/0/1
         192.168.1.0/24 is variably subnetted, 2 subnets, 2 masks
    C       192.168.1.0/24 is directly connected, GigabitEthernet0/1
    L       192.168.1.3/32 is directly connected, GigabitEthernet0/1
    R*   0.0.0.0/0 [120/1] via 10.2.2.2, 00:00:04, Serial0/0/1
    
ping-запросы с компьютера PC-A от каждого интерфейса на маршрутизаторах R1, R2 и R3 завершились удачно

ping-запросы с компьютера PC-C от каждого интерфейса на маршрутизаторах R1, R2 и R3 завершились удачно

### Часть 2:	Настройка обеспечения избыточности на первом хопе с помощью HSRP

