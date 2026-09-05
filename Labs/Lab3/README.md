# Настроить IS-IS для Underlay сети

## Цель:

1. Настроите ISIS в Underlay сети, для IP связанности между всеми сетевыми устройствами.
2. Зафиксируете в документации - план работы, адресное пространство, схему сети, конфигурацию устройств
3. Убедитесь в наличии IP связанности между устройствами в ISIS домене

Стенд был развернут на эмуляторе EVE-NG Community Edition 6.2.0-4. Для работы использовались следующие образы:
- Cisco IOL (x86_64_crb_linux_l2-adventerprisek9-ms.bin)
- Arista vEOS Switch (veos-4.34.0F)

### Топология сети

![alt-текст](https://github.com/DND-tech/Otus/blob/main/Labs/Lab1/topology.png "Текст заголовка логотипа 1")

#### План адресации
Под данную схему была выделяна сеть по 16 маске. Внутри /16 были нарезаны следующие сети:

- 10.0.0.0/24 - Loopback0 pool
- 10.0.1.0/24 - Loopback1 / VTEP
- 10.0.16.0/20 - P2P Links
- 10.0.32.0/19 - Infra/Services

Доп. инфа по интерфейсам:

- Loopback используют /32 адреса
- p2p используют /31 адреса

#### IP-план

| Device | Port | IP | Comment |
| -------|:------|----|--------|
| Spine1 | lo | 10.0.0.1 |  |
| Spine1 | lo1 | 10.0.1.1  |  |  |
| Spine1 | Eth1 | 10.0.16.0/31|  |  |
| Spine1 | Eth2 | 10.0.16.2/31 |  |  |
| Spine1 | Eth3 | 10.0.16.4/31 |  |  |
| Spine2 | lo | 10.0.0.2 |  |
| Spine2 | lo1 | 10.0.1.2  |  |  |
| Spine2 | Eth1 | 10.0.16.6/31|  |  |
| Spine2 | Eth2 | 10.0.16.8/31 |  |  |
| Spine2 | Eth3 | 10.0.16.10/31 |  |  |
| Leaf1 | lo | 10.0.0.3 |  |
| Leaf1 | lo1 | 10.0.1.3  |  |  |
| Leaf1 | Eth1 | 10.0.16.1/31|  |  |
| Leaf1 | Eth2 | 10.0.16.7/31 |  |  |
| Leaf2 | lo | 10.0.0.4 |  |
| Leaf2 | lo1 | 10.0.1.4  |  |  |
| Leaf2 | Eth1 | 10.0.16.3/31|  |  |
| Leaf2 | Eth2 | 10.0.16.9/31 |  |  |
| Leaf3 | lo | 10.0.0.5 |  |
| Leaf3 | lo1 | 10.0.1.5  |  |  |
| Leaf3 | Eth1 | 10.0.16.5/31|  |  |
| Leaf3 | Eth2 | 10.0.16.11/31 |  |  |

#### Параметры IS-IS

В конфигурации IS-IS будут настроены следующие параметры:
- Area: 0001
- Instance: UNDERLAY
- Level-1-2 IS-IS
- Network type P2P
- Passive-interface default
- На Leaf устройствах будет включен mstp
- NET:

```
Spine1 = 49.0001.0000.0000.0001.00
Spine2 = 49.0001.0000.0000.0002.00
Leaf1  = 49.0001.0000.0000.0003.00
Leaf2  = 49.0001.0000.0000.0004.00
Leaf3  = 49.0001.0000.0000.0005.00
```

#### Конфигурация на устройствах

```
Spine1

hostname spine1
!
interface Ethernet1
   mtu 9000
   no switchport
   ip address 10.0.16.0/31
   isis enable UNDERLAY
   isis network point-to-point
!
interface Ethernet2
   mtu 9000
   no switchport
   ip address 10.0.16.2/31
   isis enable UNDERLAY
   isis network point-to-point
!
interface Ethernet3
   mtu 9000
   no switchport
   ip address 10.0.16.4/31
   isis enable UNDERLAY
   isis network point-to-point
!
interface Loopback0
   ip address 10.0.0.1/32
   isis enable UNDERLAY
!
interface Loopback1
   ip address 10.0.1.1/32
   isis enable UNDERLAY
!
interface Management1
!
ip routing
!
router isis UNDERLAY
   net 49.0001.0000.0000.0001.00
   !
   address-family ipv4 unicast
!
!
Spine2

hostname spine2
!
interface Ethernet1
   mtu 9000
   no switchport
   ip address 10.0.16.6/31
   isis enable UNDERLAY
   isis network point-to-point
!
interface Ethernet2
   mtu 9000
   no switchport
   ip address 10.0.16.8/31
   isis enable UNDERLAY
   isis network point-to-point
!
interface Ethernet3
   mtu 9000
   no switchport
   ip address 10.0.16.10/31
   isis enable UNDERLAY
   isis network point-to-point
!
interface Loopback0
   ip address 10.0.0.2/32
   isis enable UNDERLAY
!
interface Loopback1
   ip address 10.0.1.2/32
   isis enable UNDERLAY
!
interface Management1
!
ip routing
!
router isis UNDERLAY
   net 49.0001.0000.0000.0002.00
   !
   address-family ipv4 unicast
!

Leaf1

hostname Leaf1
!
spanning-tree mode mstp
!
interface Ethernet1
   mtu 9000
   no switchport
   ip address 10.0.16.1/31
   isis enable UNDERLAY
   isis network point-to-point
!
interface Ethernet2
   mtu 9000
   no switchport
   ip address 10.0.16.7/31
   isis enable UNDERLAY
   isis network point-to-point
!
interface Loopback0
   ip address 10.0.0.3/32
   isis enable UNDERLAY
!
interface Loopback1
   ip address 10.0.1.3/32
   isis enable UNDERLAY
!
interface Management1
!
ip routing
!
router isis UNDERLAY
   net 49.0001.0000.0000.0003.00
   !
   address-family ipv4 unicast
!

Leaf2

hostname Leaf2
!
spanning-tree mode mstp
!

interface Ethernet1
   mtu 9000
   no switchport
   ip address 10.0.16.3/31
   isis enable UNDERLAY
   isis network point-to-point
!
interface Ethernet2
   mtu 9000
   no switchport
   ip address 10.0.16.9/31
   isis enable UNDERLAY
   isis network point-to-point
!
interface Loopback0
   ip address 10.0.0.4/32
   isis enable UNDERLAY
!
interface Loopback1
   ip address 10.0.1.4/32
   isis enable UNDERLAY

!
ip routing
!
router isis UNDERLAY
   net 49.0001.0000.0000.0004.00
   !
   address-family ipv4 unicast
!
Leaf3

hostname Leaf3
!
spanning-tree mode mstp
!
interface Ethernet1
   mtu 9000
   no switchport
   ip address 10.0.16.5/31
   isis enable UNDERLAY
   isis network point-to-point
!
interface Ethernet2
   mtu 9000
   no switchport
   ip address 10.0.16.11/31
   isis enable UNDERLAY
   isis network point-to-point
!
interface Loopback0
   ip address 10.0.0.5/32
   isis enable UNDERLAY
!
interface Loopback1
   ip address 10.0.1.5/32
   isis enable UNDERLAY
!
interface Management1
!
ip routing
!
router isis UNDERLAY
   net 49.0001.0000.0000.0005.00
   !
   address-family ipv4 unicast
!
```
Проверим работу интерфейсов, соседей и маршруты IS-IS на Spine1 и Spine2.

* Spine1

![alt-текст](https://github.com/DND-tech/Otus/blob/main/Labs/Lab3/is-is_spine1.png "Spine1")


![alt-текст](https://github.com/DND-tech/Otus/blob/main/Labs/Lab3/is-is_routes_spine1.png "Spine1")


* Spine2

![alt-текст](https://github.com/DND-tech/Otus/blob/main/Labs/Lab3/is-is_spine1.png "Spine2")


![alt-текст](https://github.com/DND-tech/Otus/blob/main/Labs/Lab3/is-is_routes_spine2.png "Spine1")

 По результату работу наблюдаю сетевую доступность между spine/leaf нодами.