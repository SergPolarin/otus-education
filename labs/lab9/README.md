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

| Host  | Interface  | Ip-address     | IPV6-address          |
|-------|------------|----------------|-----------------------|
| R25   | e0/3       | 60.25.28.1/30  | 2520:AA60:25:28::1/64 |
| R25   | e0/1       | 60.25.27.1/30  | 2520:AA60:25:27::1/64 |
| R25   | e0/2       | 52.25.26.1/30  | 2520:AA60:25:26::1/64 |
| R25   | link-local | -              | FE80::25              |
| R26   | e0/1       | 60.26.28.1/30  | 2520:AA60:26:28::1/64 |
| R26   | e0/2       | 52.25.26.2/30  | 2520:AA60:25:26::2/64 |
| R26   | link-local | -              | FE80::26              |
| R27   | e0/0       | 60.25.27.2/30  | 2520:AA60:25:27::2/64 |
| R27   | link-local | -              | FE80::27              |
| R28   | e0/0       | 60.26.28.2/30  | 2520:AA60:26:28::2/64 |
| R28   | e0/1       | 60.25.28.2/30  | 2520:AA60:25:28::2/64 |
| R28   | e0/2       | 192.168.1.1/24 | 2074:AA74:28:28::1/64 |
| R28   | link-local | -              | FE80::28              |
| VPC30 |            | 192.168.1.2/24 | 2074:AA74:28:28::2/64 |
| VPC31 |            | 192.168.1.3/24 | 2074:AA74:28:28::3/64 |

### Настроить политику маршрутизации для сетей офиса

Вывод команды sh ip route static

R25

      sh ip route static
      60.0.0.0/8 is variably subnetted, 5 subnets, 2 masks 
      S        60.26.28.0/30 [1/0] via 52.25.26.2
      S     192.168.1.0/24 [1/0] via 60.25.28.2
      [1/0] via 52.25.26.2  

      sh ipv6 route static
      S   2074:AA74:28:28::/64 [1/0]  
      via 2520:AA60:25:28::2 
      via 2520:AA60:25:26::2  
      S   2520:AA60:26:28::/64 [1/0]   
      via 2520:AA60:25:26::2  

R26

      sh ip route static
      60.0.0.0/8 is variably subnetted, 4 subnets, 2 masks  
      S        60.25.27.0/30 [1/0] via 52.25.26.1
      S        60.25.28.0/30 [1/0] via 52.25.26.1
      S     192.168.1.0/24 [1/0] via 60.26.28.2 
      
      sh ipv6 route static
      S   2074:AA74:28:28::/64 [1/0]
      via 2520:AA60:26:28::2 
      via 2520:AA60:25:26::1  
      S   2520:AA60:25:27::/64 [1/0] 
      via 2520:AA60:25:26::1 
     
 R27  Настройть для офиса Лабытнанги маршрут по-умолчанию
 
      S*    0.0.0.0/0 [1/0] via 60.25.27.1 
      S   ::/0 [1/0]   
      via 2520:AA60:25:27::1  
      
 R28
 
       sh ip route static
       52.0.0.0/30 is subnetted, 1 subnets     
       S        52.25.26.0 [1/0] via 60.26.28.1 
       [1/0] via 60.25.28.1                                                                                      
       60.0.0.0/8 is variably subnetted, 5 subnets, 2 masks 
       S        60.25.27.0/30 [1/0] via 60.26.28.1  
       [1/0] via 60.25.28.1  

       sh ipv6 route static
       S   2520:AA60:25:26::/64 [1/0]
       via 2520:AA60:26:28::1
       via 2520:AA60:25:28::1
       S   2520:AA60:25:27::/64 [1/0]
       via 2520:AA60:26:28::1
       via 2520:AA60:25:28::1

### Распределить трафик между двумя линками с провайдером

Решение:

Настройка на маршрутизаторе R28

Команды

      ---- настройка ACL -----------
      conf t
      ip access-list standard VPC30
      permit host 192.168.1.2
      deny any
      
      ipv6 access-list VPC_30    
      permit ipv6 host 2074:AA74:28:28::2 any  
      deny ipv6 any any
      
      -----настройка route-map------
      route-map VPC30 permit 10
      match ip address VPC30
      match ipv6 address VPC_30
      set ip next-hop 60.26.28.1
      set ipv6 next-hop 2520:AA60:26:28::1
      
      ----настройка int e0/2--------
     int e0/2
     ip policy route-map VPC30
 
Вывод

     R28#sh run | s route-m  
     ip policy route-map VPC30  
     route-map VPC30 permit 10  
     match ip address VPC30    
     match ipv6 address VPC_30    
     set ip next-hop verify-availability 60.26.28.1 10 track 1 
     set ip next-hop verify-availability 60.25.28.1 20 track 2 
     set ipv6 next-hop 2520:AA60:26:28::1  
      
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
       
       
       VPC31> trace 2520:aa60:25:27::2    
       trace to 2520:aa60:25:27::2, 64 hops max  
       1 2074:aa74:28:28::1   0.480 ms  0.213 ms  0.239 ms  
       2 2520:aa60:25:28::1   0.398 ms  *  *     
       3 2520:aa60:25:27::2   0.810 ms  0.614 ms  0.515 ms
       
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

      VPC30> trace 2520:aa60:25:27::2   
      trace to 2520:aa60:25:27::2, 64 hops max  
      1 2074:aa74:28:28::1   7.955 ms  0.350 ms  0.253 ms 
      2 2520:aa60:26:28::1   0.811 ms  0.580 ms  0.536 ms 
      3 2520:aa60:25:26::1   0.711 ms  0.590 ms  0.600 ms 
      4 2520:aa60:25:27::2   0.863 ms  0.617 ms  0.751 ms  


### Настроить отслеживание линка через технологию IP SLA

Настройка на маршрутизаторе R28

Команды

      conf t
      ---- удаляем предыдущую настройку next-hop----
      route-map VPC30 permit 10
      no set ip next-hop 60.26.28.1
      exit
      
      --- настраиваем ip sla 1 ---------------------
      ip sla 1
      icmp-echo 60.26.28.1 source-ip 60.26.28.2
      threshold 2
      frequency 2
      timeout 3
      exit
      ip sla schedule 1 life forever start-time now
      track 1 ip sla 1 reachability
      
            --- настраиваем ip sla 2 ---------------------
      ip sla 2
      icmp-echo 60.25.28.1 source-ip 60.25.28.2
      threshold 2
      frequency 3
      timeout 1000
      exit
      ip sla schedule 2 life forever start-time now
      track 2 ip sla 2 reachability
      
      ------ настраиваем route-map VPC30 -----------
      route-map VPC30 permit 10
      set ip next-hop verify-availability 60.26.28.1 10 track 1
      set ip next-hop verify-availability 60.25.28.1 20 track 2
      
      
  Проверка настройки на VPC30
  
  Первоначальная трассировка:
  
      VPC30> trace 60.25.27.2 
      trace to 60.25.27.2, 8 hops max, press Ctrl+C to stop  
      1   192.168.1.1   0.538 ms  0.366 ms  0.379 ms  
      2   60.26.28.1   0.551 ms  0.518 ms  0.528 ms  
      3   52.25.26.1   0.655 ms  0.526 ms  0.519 ms 
      4   *60.25.27.2   0.605 ms (ICMP type:3, code:3, Destination port unreachable)  * 
      
Трассировка после выключение интерфейса e0/2 на маршрутизаторе R26
      
      VPC30> trace 60.25.27.2    
      trace to 60.25.27.2, 8 hops max, press Ctrl+C to stop  
      1   192.168.1.1   0.530 ms  0.325 ms  0.295 ms  
      2     *  *  *    
      3   *60.25.27.2   0.853 ms (ICMP type:3, code:3, Destination port unreachable)  *
      
ICMP-запросы при выключенном интерфейсе e0/2 на маршрутизаторе R26

      VPC30> ping 60.25.27.2  
      84 bytes from 60.25.27.2 icmp_seq=1 ttl=253 time=0.777 ms  
      84 bytes from 60.25.27.2 icmp_seq=2 ttl=253 time=0.896 ms 
      84 bytes from 60.25.27.2 icmp_seq=3 ttl=253 time=0.802 ms  
      84 bytes from 60.25.27.2 icmp_seq=4 ttl=253 time=0.940 ms  
      84 bytes from 60.25.27.2 icmp_seq=5 ttl=253 time=0.987 ms 
