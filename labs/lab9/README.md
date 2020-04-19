# Маршрутизация на основе политик PBR

## Топология

![](topology.png)

## Задачи

1. Настроить политику маршрутизации для сетей офиса
2. Распределить трафик между двумя линками с провайдером
3. Настроить отслеживание линка через технологию IP SLA
4. Настройть для офиса Лабытнанги маршрут по-умолчанию
5. План работы и изменения зафиксированы в документации 

## Адресация

| Host  | Interface | Ip-address     |
|-------|-----------|----------------|
| R25   | e0/3      | 60.25.28.1/30  |
| R25   | e0/1      | 60.25.27.1/30  |
| R25   | e0/2      | 52.25.26.1/30  |
| R26   | e0/1      | 60.26.28.1/30  |
| R26   | e0/2      | 52.25.26.2/30  |
| R28   | e0/0      | 60.26.28.2/30  |
| R28   | e0/1      | 60.25.28.2/30  |
| R28   | e0/2      | 192.168.1.1/24 |
| VPC30 |           | 192.168.1.2/24 |
| VPC31 |           | 192.168.1.3/24 |

### Параметры статической марщрутизации на маршрутизаторах

Вывод команды sh ip route static

R25

      60.0.0.0/8 is variably subnetted, 5 subnets, 2 masks 
      S        60.26.28.0/30 [1/0] via 52.25.26.2
      S     192.168.1.0/24 [1/0] via 60.25.28.2
      [1/0] via 52.25.26.2  

R26

      60.0.0.0/8 is variably subnetted, 4 subnets, 2 masks  
      S        60.25.27.0/30 [1/0] via 52.25.26.1
      S        60.25.28.0/30 [1/0] via 52.25.26.1
      S     192.168.1.0/24 [1/0] via 60.26.28.2 
     
 R27
 
      S*    0.0.0.0/0 [1/0] via 60.25.27.1 
      
 R28
 
       52.0.0.0/30 is subnetted, 1 subnets     
       S        52.25.26.0 [1/0] via 60.26.28.1 
       [1/0] via 60.25.28.1                                                                                      
       60.0.0.0/8 is variably subnetted, 5 subnets, 2 masks 
       S        60.25.27.0/30 [1/0] via 60.26.28.1  
       [1/0] via 60.25.28.1  


### Распределить трафик между двумя линками с провайдером

Решение:

Настройка на маршрутизаторе R28

Команды

      ---- настройка ACL -----------
      conf t
      ip access-list standard VPC30
      permit host 192.168.1.2
      deny any
      
      -----настройка route-map------
      route-map VPC30 permit 10
      match ip address VPC30
      set ip next-hop 60.26.28.1
      
      ----настройка int e0/2--------
     int e0/2
     ip policy route-map VPC30
 
Вывод

      R28(config-route-map)#do sh run | s route-map
      ip policy route-map VPC30
      route-map VPC30 permit 10 
      match ip address VPC30
      set ip next-hop 60.26.28.1
      
Трассировка маршрута с VPC31

До настройки PBR

      VPCS> trace 60.25.27.2
      trace to 60.25.27.2, 8 hops max, press Ctrl+C to stop
       1   192.168.1.1   0.326 ms  0.279 ms  0.333 ms
       2   60.25.28.1   0.548 ms  0.468 ms  0.507 ms
       3   *60.25.27.2   0.779 ms (ICMP type:3, code:3, Destination port unreachable)  *

После настройки PBR

      VPCS> trace 60.25.27.2
      trace to 60.25.27.2, 8 hops max, press Ctrl+C to stop
       1   192.168.1.1   0.372 ms  0.374 ms  0.359 ms
       2   60.25.28.1   0.525 ms  0.434 ms  0.405 ms
       3   *60.25.27.2   0.669 ms (ICMP type:3, code:3, Destination port unreachable)  *
       
Трассировка маршрута с VPC30

До настройки PBR

      VPCS> trace 60.25.27.2
      trace to 60.25.27.2, 8 hops max, press Ctrl+C to stop 
      1   192.168.1.1   0.301 ms  0.311 ms  0.392 ms  
      2   60.25.28.1   0.609 ms  0.629 ms  0.452 ms     
      3   *60.25.27.2   0.692 ms (ICMP type:3, code:3, Destination port unreachable)  * 

После настройки PBR

      VPCS> trace 60.25.27.2     
      trace to 60.25.27.2, 8 hops max, press Ctrl+C to stop 
      1   192.168.1.1   0.475 ms  0.465 ms  0.320 ms   
      2   60.26.28.1   0.570 ms  0.438 ms  0.408 ms             ## Изменился next-hop
      3   52.25.26.1   0.688 ms  0.581 ms  0.548 ms             ## Добавился еще один маршрут
      4   *60.25.27.2   0.743 ms (ICMP type:3, code:3, Destination port unreachable)  * 
