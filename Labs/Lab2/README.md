# Настроить OSPF для Underlay сети

## Цель:

1. Настроите OSPF в Underlay сети, для IP связанности между всеми сетевыми устройствами.
2. Зафиксируете в документации - план работы, адресное пространство, схему сети, конфигурацию устройств
3. Убедитесь в наличии IP связанности между устройствами в OSFP домене

Стенд был развернут на эмуляторе EVE-NG Community Edition 6.2.0-4. Для работы использовались следующие образы:
- Cisco IOL (x86_64_crb_linux_l2-adventerprisek9-ms.bin)
- Arista vEOS Switch (veos-4.34.0F)

### Топология сети

![alt-текст](https://github.com/DND-tech/Otus/blob/main/Labs/Lab1/topology.png "Текст заголовка логотипа 1")
Схему сети и адресацию будем брать из [Лабораторной работы №1.Проектирование адресного пространства](https://github.com/DND-tech/Otus/tree/main/Labs/Lab1)

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
| Spane1 | lo | 10.0.0.1 |  |
| Spane1 | lo1 | 10.0.1.1  |  |  |
| Spane1 | Eth1 | 10.0.16.0/31|  |  |
| Spane1 | Eth2 | 10.0.16.2/31 |  |  |
| Spane1 | Eth3 | 10.0.16.4/31 |  |  |
| Spane2 | lo | 10.0.0.2 |  |
| Spane2 | lo1 | 10.0.1.2  |  |  |
| Spane2 | Eth1 | 10.0.16.6/31|  |  |
| Spane2 | Eth2 | 10.0.16.8/31 |  |  |
| Spane2 | Eth3 | 10.0.16.10/31 |  |  |
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


#### Конфигурация на устройствах

```
Spine1

hostname spine1

interface Ethernet1
   no switchport
   ip address 10.0.16.0/31
!
interface Ethernet2
   no switchport
   ip address 10.0.16.2/31
!
interface Ethernet3
   no switchport
   ip address 10.0.16.4/31
!
interface Loopback0
   ip address 10.0.0.1/32
!
interface Loopback1
   ip address 10.0.1.1/32
   

Spine2

hostname spine2

interface Ethernet1
   no switchport
   ip address 10.0.16.6/31
!
interface Ethernet2
   no switchport
   ip address 10.0.16.8/31
!
interface Ethernet3
   no switchport
   ip address 10.0.16.10/31
!
interface Loopback0
   ip address 10.0.0.2/32
!
interface Loopback1
   ip address 10.0.1.2/32
   

Leaf1

hostname Leaf1

interface Ethernet1
   no switchport
   ip address 10.0.16.1/31
!
interface Ethernet2
   no switchport
   ip address 10.0.16.7/31
!
interface Ethernet3
!
interface Loopback0
   ip address 10.0.0.3/32
!
interface Loopback1
   ip address 10.0.1.3/32
!

Leaf2

hostname Leaf2

interface Ethernet1
   no switchport
   ip address 10.0.16.3/31
!
interface Ethernet2
   no switchport
   ip address 10.0.16.9/31
!
interface Ethernet3
!
interface Loopback0
   ip address 10.0.0.4/32
!
interface Loopback1
   ip address 10.0.1.4/32
!


Leaf3

hostname Leaf3

interface Ethernet1
   no switchport
   ip address 10.0.16.5/31
!
interface Ethernet2
   no switchport
   ip address 10.0.16.11/31
!

interface Loopback0
   ip address 10.0.0.5/32
!
interface Loopback1
   ip address 10.0.1.5/32
!
```
