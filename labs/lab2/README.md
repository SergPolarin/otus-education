## Лабораторная работа. Развертывание коммутируемой сети с резервными каналами

### Топология

![](topology.png)

### Таблица адресации

|Заголовок таблицы	|Интерфейс |IP-адрес	  |Маска подсети|
|:------------------|:---------|:-----------|:------------|
|S1	                |VLAN 1 	 |192.168.1.1 |255.255.255.0|
|S2	                |VLAN 1 	 |192.168.1.2 |255.255.255.0|
|S3	                |VLAN 1 	 |192.168.1.3 |255.255.255.0|

### Цели:

1. Создание сети и настройка основных параметров устройства
2. Выбор корневого моста
3. Наблюдение за процессом выбора протоколом STP порта, исходя из стоимости портов
4. Наблюдение за процессом выбора протоколом STP порта, исходя из приоритета портов

### Часть 1:	Создание сети и настройка основных параметров устройства

#### Шаг 3 Настройте базовые параметры каждого коммутатора
Building configuration...

hostname S1

enable secret 5 $1$mERr$9cTjUIEqNGurQiFU.ZeCi1

no ip domain-lookup

spanning-tree mode pvst

spanning-tree extend system-id

...

interface FastEthernet0/1

...

interface Vlan1

 ip address 192.168.1.1 255.255.255.0
 
banner motd ^C UNAUTHORISED ACCESS PROHIBITED ^C

line con 0
 password 7 0822455D0A16
 logging synchronous
 
line vty 0 4
 password 7 0822455D0A16
 logging synchronous
 login
line vty 5 15

 login
 
end
