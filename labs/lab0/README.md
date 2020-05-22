# Протокол IP. Документация

### Таблица адресации

| ISP        | ASN     | Host | Discription | Interface | IPv4              | IPv6                  | Link-local |
|------------|---------|------|-------------|-----------|-------------------|-----------------------|------------|
|            |         | R12  | toLAN       | e0/0      | 172.16.0.12       | ???                   |            |
|            | AS 1001 | R12  | toR14       | e0/2      | 77.12.14.1/30     | 2077:12:14::1/64      | FE80::12   |
|            |         | R12  | toR15       | e0/3      | 77.12.15.1/30     | 2077:12:15::1/64      |            |
|            |         | R13  | toLAN       | e0/0      | 172.16.0.13       | ???                   |            |
|            |         | R13  | toR15       | e0/2      | 77.13.15.1/30     | 2077:13:15::1/64      | FE80::13   |
|            |         | R13  | toR14       | e0/3      | 77.13.14.1/30     | 2077:13:14::1/64      |            |
|            |         | R14  | toR12       | e0/0      | 77.12.14.2/30     | 2077:12:14::2/64      | FE80::14   |
|            |         | R14  | toR13       | e0/1      | 77.13.14.2/30     | 2077:13:14::2/64      |            |
|            |         | R14  | toR22       | e0/2      | 43.22.14.2/30     | 2043:22:14::2/64      |            |
|            |         | R14  | toR19       | e0/3      | 77.14.19.1/30     | 2077:14:19::1/64      |            |
|            |         | R15  | toR13       | e0/0      | 77.13.15.2/30     | 2077:13:15::2/64      | FE80::15   |
|            |         | R15  | toR12       | e0/1      | 77.12.15.2/30     | 2077:12:15::2/64      |            |
|            |         | R15  | toR21       | e0/2      | 57.21.15.2/30     | 2057:21:15::2/64      |            |
|            |         | R15  | toR20       | e0/3      | 77.15.20.1/30     | 2077:15:20::1/64      |            |
|            |         | R19  | toR14       | e0/0      | 77.14.19.2/30     | 2077:14:19::2/64      | FE80::19   |
|            |         | R20  | toR15       | e0/0      | 77.15.20.2/30     | 2077:15:20::2/64      | FE80::20   |
| Ламас      | AS 301  | R21  | toR15       | e0/0      | 57.21.15.1/30     | 2057:21:15::1/64      | FE80::21   |
|            |         | R21  | toR22       | e0/1      | 57.21.22.1/30     | 2057:21:22::1/64      |            |
|            |         | R21  | toR24       | e0/2      | 60.24.21.2/30     | 2520:AA60:24:21::2/64 |            |
| Киторн     | AS 101  | R22  | toR14       | e0/0      | 43.22.14.1/30     | 2043:22:14::1/64      | FE80::22   |
|            |         | R22  | toR21       | e0/1      | 57.21.22.2/30     | 2057:21:22::2/64      |            |
|            |         | R22  | toR23       | e0/2      | 60.23.22.2/30     | 2520:AA60:23:22::2/64 |            |
| Триада     | AS 520  | R23  | toR22       | e0/0      | 60.23.22.1/30     | 2520:AA60:23:22::1/64 | FE80::23   |
|            |         | R23  | toR25       | e0/1      | 52.23.25.1/30     | 2520:AA60:23:25::1/64 |            |
|            |         | R23  | toR24       | e0/2      | 52.23.24.1/30     | 2520:AA60:23:24::1/64 |            |
|            |         | R24  | toR21       | e0/0      | 60.24.21.1/30     | 2520:AA60:24:21::1/64 | FE80::24   |
|            |         | R24  | toR26       | e0/1      | 52.24.26.1/30     | 2520:AA60:24:26::1/64 |            |
|            |         | R24  | toR23       | e0/2      | 52.23.24.2/30     | 2520:AA60:23:24::2/64 |            |
|            |         | R24  | toR18       | e0/3      | 60.24.18.1/30     | 2520:AA60:24:18::1/64 |            |
|            |         | R25  | toR23       | e0/0      | 52.23.25.2/30     | 2520:AA60:23:25::2/64 | FE80::25   |
|            |         | R25  | toR28       | e0/3      | 60.25.28.1/30     | 2520:AA60:25:28::1/64 |            |
|            |         | R25  | toR27       | e0/1      | 60.25.27.1/30     | 2520:AA60:25:27::1/64 |            |
|            |         | R25  | toR26       | e0/2      | 52.25.26.1/30     | 2520:AA60:25:26::1/64 |            |
|            |         | R26  | toR24       | e0/0      | 52.24.26.2/30     | 2520:AA60:24:26::2/64 | FE80::26   |
|            |         | R26  | toR28       | e0/1      | 60.26.28.1/30     | 2520:AA60:26:28::1/64 |            |
|            |         | R26  | toR25       | e0/2      | 52.25.26.2/30     | 2520:AA60:25:26::2/64 |            |
|            |         | R26  | toR18       | e0/3      | 60.26.18.1/30     | 2520:AA60:26:18::1/64 |            |
| Лабытнанги |         | R27  | toR25       |           | 60.25.27.2/30     | 2520:AA60:25:27::2/64 | FE80::27   |
| Чокурдах   |         | R28  | toR26       | e0/0      | 60.26.28.2/30     | 2520:AA60:26:28::2/64 | FE80::28   |
|            |         | R28  | toR25       | e0/1      | 60.25.28.2/30     | 2520:AA60:25:28::2/64 |            |
|            |         | R28  | toLAN       | e0/2      | 192.168.1.1/24    | 2074:AA74:28:28::1/64 |            |
| СПб        | AS 2042 | R16  | toLAN       | e0/0      | 192.168.20.253/24 | ???                   | FE80::16   |
|            |         | R16  | toR18       | e0/1      | 78.16.18.2/30     | 2078:16:18::2/64      |            |
|            |         | R16  | toR32       | e0/3      | 78.16.32.1/30     | 2078:16:32::1/64      |            |
|            |         | R17  | toLAN       | e0/0      | 192.168.20.254/24 | ???                   | FE80::17   |
|            |         | R17  | toR18       | e0/1      | 78.17.18.2/30     | 2078:17:18::2/64      |            |
|            |         | R18  | toR16       | e0/0      | 78.16.18.1/30     | 2078:16:18::1/64      | FE80::18   |
|            |         | R18  | toR17       | e0/1      | 78.17.18.1/30     | 2078:17:18::1/64      |            |
|            |         | R18  | toR24       | e0/2      | 60.24.18.2/30     | 2520:AA60:24:18::2/64 |            |
|            |         | R18  | toR26       | e0/3      | 60.26.18.2/30     | 2520:AA60:26:18::2/64 |            |
|            |         | R32  | toR16       | e0/0      | 78.16.32.2/30     | 2078:16:32::2/64      | FE80::32   |

### Задачи:

1. Разработать и задокументировать адресное пространство для лабораторного стенда.
2. Настроить ip адреса на каждом активном порту
4. Настроить каждый VPC в любом офисе в своем VLAN.
5. Настроить VLAN управления для сетевых устройств
6. Настроить сети офисов так, чтобы не возникало broadcast штормов, а использование линков было максимально оптимизировано
7. Использовать ipv4 и ipv6

## R12

    Building configuration...

    Current configuration : 1554 bytes
    !
    version 15.2
    service timestamps debug datetime msec
    service timestamps log datetime msec
    no service password-encryption
    !
    hostname R12
    !
    boot-start-marker
    boot-end-marker
    !
    !
    !
    no aaa new-model
    clock timezone EET 2 0
    mmi polling-interval 60
    no mmi auto-configure
    no mmi pvc
    mmi snmp-timeout 180
  
    !
    ip cef
    ipv6 unicast-routing
    ipv6 cef
    !
    multilink bundle-name authenticated
    !
   
    !
    redundancy
    !
    !
    interface Ethernet0/0
     ip address 172.16.0.12 255.255.0.0
     standby version 2
     standby 1 ip 172.16.0.1
     standby 1 priority 150
     standby 1 preempt
    !
    interface Ethernet0/1
     no ip address
     shutdown
    !
    interface Ethernet0/2
     ip address 77.12.14.1 255.255.255.252
     ipv6 address FE80::12 link-local
     ipv6 address 2077:12:14::1/64
     ipv6 ospf 1 area 0
    !
    interface Ethernet0/3
     ip address 77.12.15.1 255.255.255.252
     ipv6 address FE80::12 link-local
     ipv6 address 2077:12:15::1/64
     ipv6 ospf 1 area 0
    !
    interface Ethernet1/0
     no ip address
     shutdown
    !
    interface Ethernet1/1
     no ip address
     shutdown
    !
    interface Ethernet1/2
     no ip address
     shutdown
    !
    interface Ethernet1/3
     no ip address
     shutdown
    !
    router ospf 1
     router-id 12.12.12.12
     area 10 stub
     network 77.12.14.0 0.0.0.3 area 0
     network 77.12.15.0 0.0.0.3 area 0
     network 172.16.0.0 0.0.0.255 area 10
    !
    ip forward-protocol nd
    !
    !
    no ip http server
    no ip http secure-server
    !
    ipv6 router ospf 1
     router-id 12.12.12.12
     area 10 stub
    !
    control-plane

    !
    line con 0
     logging synchronous
    line aux 0
    line vty 0 4
     login
     transport input all
    !
    !
    end

## R13


    Building configuration...

    Current configuration : 1513 bytes
    !
    version 15.2
    service timestamps debug datetime msec
    service timestamps log datetime msec
    no service password-encryption
    !
    hostname R13
    !
    boot-start-marker
    boot-end-marker
    !
    !
    !
    no aaa new-model
    clock timezone EET 2 0
    mmi polling-interval 60
    no mmi auto-configure
    no mmi pvc
    mmi snmp-timeout 180
    !
    !
    !
    ip cef
    ipv6 unicast-routing
    ipv6 cef
    !
    multilink bundle-name authenticated
    !
    !
    redundancy
    !
    !
    interface Ethernet0/0
     ip address 172.16.0.13 255.255.0.0
     standby version 2
     standby 1 ip 172.16.0.1
    !
    interface Ethernet0/1
     no ip address
     shutdown
    !
    interface Ethernet0/2
     ip address 77.13.15.1 255.255.255.252
     ipv6 address FE80::13 link-local
     ipv6 address 2077:13:15::1/64
     ipv6 ospf 1 area 0
    !
    interface Ethernet0/3
     ip address 77.13.14.1 255.255.255.252
     ipv6 address FE80::13 link-local
     ipv6 address 2077:13:14::1/64
     ipv6 ospf 1 area 0
    !
    interface Ethernet1/0
     no ip address
     shutdown
    !
    interface Ethernet1/1
     no ip address
     shutdown
    !
    interface Ethernet1/2
     no ip address
     shutdown
    !
    interface Ethernet1/3
     no ip address
     shutdown
    !
    router ospf 1
     router-id 13.13.13.13
     area 10 stub
     network 77.13.14.0 0.0.0.3 area 0
     network 77.13.15.0 0.0.0.3 area 0
     network 172.16.0.0 0.0.255.255 area 10
    !
    ip forward-protocol nd
    !
    !
    no ip http server
    no ip http secure-server
    !
    ipv6 router ospf 1
     router-id 13.13.13.13
     area 10 stub
    !
    !
    !
    !
    control-plane
    !
    !
    !
    !
    !
    !
    !
    line con 0
     logging synchronous
    line aux 0
    line vty 0 4
     login
     transport input all
    !
    !
    end
    
## SW4

    Building configuration...

    Current configuration : 1388 bytes
    !
    ! Last configuration change at 22:10:31 EET Thu May 21 2020
    !
    version 15.2
    service timestamps debug datetime msec
    service timestamps log datetime msec
    no service password-encryption
    service compress-config
    !
    hostname SW4
    !
    boot-start-marker
    boot-end-marker
    !
    !
    !
    no aaa new-model
    clock timezone EET 2 0
    !
    !
    !
    !
    !
    !
    !
    !
    ip cef
    no ipv6 cef
    !
    !
    !
    spanning-tree mode rapid-pvst
    spanning-tree extend system-id
    !
    vlan internal allocation policy ascending
    !
    !
    !
    !
    !
    !
    !
    !
    !
    !
    !
    !
    !
    interface Port-channel1
    !
    interface Ethernet0/0
     switchport trunk encapsulation dot1q
    !
    interface Ethernet0/1
     switchport access vlan 10
     switchport mode access
    !
    interface Ethernet0/2
     channel-group 1 mode on
    !
    interface Ethernet0/3
     channel-group 1 mode on
    !
    interface Ethernet1/0
    !
    interface Ethernet1/1
    !
    interface Ethernet1/2
    !
    interface Ethernet1/3
    !
    interface Vlan1
     ip address 192.168.1.14 255.255.255.0
    !
    interface Vlan10
     ip address 172.16.0.3 255.255.0.0
    !
    interface Vlan11
     ip address 192.168.11.253 255.255.255.0
     standby version 2
     standby 1 ip 192.168.11.1
     standby 1 priority 150
    !
    interface Vlan17
     ip address 192.168.17.253 255.255.255.0
     standby version 2
     standby 0 preempt
     standby 1 ip 192.168.17.1
     standby 1 priority 150
    !
    ip forward-protocol nd
    !
    no ip http server
    no ip http secure-server
    !
    !
    !
    !
    !
    !
    control-plane
    !
    !
    line con 0
     logging synchronous
    line aux 0
    line vty 0 4
    !
    !
    end

## SW5

    Building configuration...

    Current configuration : 1224 bytes
    !
    ! Last configuration change at 14:35:44 EET Fri May 22 2020
    !
    version 15.2
    service timestamps debug datetime msec
    service timestamps log datetime msec
    no service password-encryption
    service compress-config
    !
    hostname SW5
    !
    boot-start-marker
    boot-end-marker
    !
    !
    !
    no aaa new-model
    clock timezone EET 2 0
    !
    !
    !
    !
    !
    !
    !
    !
    ip cef
    no ipv6 cef
    !
    !
    spanning-tree mode pvst
    spanning-tree extend system-id
    !
    vlan internal allocation policy ascending
    !
    !
    !
    !
    !
    !
    !
    !
    !
    !
    !
    !
    !
    interface Port-channel1
     switchport trunk encapsulation dot1q
     switchport mode trunk
    !
    interface Ethernet0/0
     switchport trunk encapsulation dot1q
    !
    interface Ethernet0/1
     switchport trunk encapsulation dot1q
    !
    interface Ethernet0/2
     switchport trunk encapsulation dot1q
     switchport mode trunk
     channel-group 1 mode on
    !
    interface Ethernet0/3
     switchport trunk encapsulation dot1q
     switchport mode trunk
     channel-group 1 mode on
    !
    interface Ethernet1/0
    !
    interface Ethernet1/1
    !
    interface Ethernet1/2
    !
    interface Ethernet1/3
    !
    interface Vlan1
     ip address 192.168.1.15 255.255.255.0
    !
    ip forward-protocol nd
    !
    no ip http server
    no ip http secure-server
    !
    !
    !
    !
    !
    !
    control-plane
    !
    !
    line con 0
     logging synchronous
    line aux 0
    line vty 0 4
     login
    !
    !
    end

## SW3

    Building configuration...

    Current configuration : 1223 bytes
    !
    ! Last configuration change at 22:02:14 EET Thu May 21 2020
    !
    version 15.2
    service timestamps debug datetime msec
    service timestamps log datetime msec
    no service password-encryption
    service compress-config
    !
    hostname SW3
    !
    boot-start-marker
    boot-end-marker
    !
    !
    !
    no aaa new-model
    clock timezone EET 2 0
    !
    ip cef
    no ipv6 cef
    !
    !
    !
    spanning-tree mode rapid-pvst
    spanning-tree extend system-id
    !
    vlan internal allocation policy ascending
    !
    interface Ethernet0/0
     switchport trunk encapsulation dot1q
     switchport mode trunk
    !
    interface Ethernet0/1
     switchport trunk encapsulation dot1q
     switchport mode trunk
    !
    interface Ethernet0/2
     switchport access vlan 11
     switchport mode access
    !
    interface Ethernet0/3
     switchport access vlan 17
     switchport mode access
    !
    interface Ethernet1/0
    !
    interface Ethernet1/1
    !
    interface Ethernet1/2
    !
    interface Ethernet1/3
    !
    interface Vlan1
     description MANAGEMENT
     ip address 192.168.1.11 255.255.255.0
    !
    interface Vlan11
     no ip address
    !
    interface Vlan17
     no ip address
     shutdown
    !
    ip forward-protocol nd
    !
    no ip http server
    no ip http secure-server
    !
    control-plane
    !
    !
    line con 0
     logging synchronous
    line aux 0
    line vty 0 4
     login
    !
    !
    end

## SW2

    Building configuration...

    Current configuration : 1121 bytes
    !
    ! Last configuration change at 22:01:51 EET Thu May 21 2020
    !
    version 15.2
    service timestamps debug datetime msec
    service timestamps log datetime msec
    no service password-encryption
    service compress-config
    !
    hostname SW2
    !
    boot-start-marker
    boot-end-marker
    !
    !
    !
    no aaa new-model
    clock timezone EET 2 0
    !
    !
    !
    !
    !
    !
    !
    !
    ip cef
    no ipv6 cef
    !
    !
    !
    spanning-tree mode rapid-pvst
    spanning-tree extend system-id
    !
    vlan internal allocation policy ascending
    !
    !
    !
    !
    !
    !
    !
    !
    !
    !
    !
    !
    !
    interface Ethernet0/0
     switchport trunk encapsulation dot1q
     switchport mode trunk
    !
    interface Ethernet0/1
     switchport trunk encapsulation dot1q
     switchport mode trunk
    !
    interface Ethernet0/2
     switchport access vlan 17
     switchport mode access
    !
    interface Ethernet0/3
    !
    interface Ethernet1/0
    !
    interface Ethernet1/1
    !
    interface Ethernet1/2
    !
    interface Ethernet1/3
    !
    interface Vlan1
     description MANAGEMENT
     ip address 192.168.1.12 255.255.255.0
    !
    interface Vlan17
     no ip address
    !
    ip forward-protocol nd
    !
    no ip http server
    no ip http secure-server
    !
    !
    !
    !
    !
    !
    control-plane
    !
    !
    line con 0
     logging synchronous
    line aux 0
    line vty 0 4
    !
    !
    end
