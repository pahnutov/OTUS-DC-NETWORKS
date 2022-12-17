## Домашнее задание
Построение Underlay сети (OSPF)

Цель:
- Настроить первичные данные на коммутаторах;
- Сконфигурировать динамическую маршрутизацию на базе протокола OSPF.

## Схема сети

![schemeOSPF.png](schemeOSPF.png)

## Выполнение домашней работы в EVE-NG

### Настройка коммутаторов

<details>
<summary>Конфигурация коммутатора <b>DC1-S1</b>: </summary>

```
hostname DC1-S1
feature ospf

username admin password 5 $5$EpjWybJm$5mpj3jo3pEmSllAwFICLfBi/7uvJmNQ0eheQXqc75kD  role network-admin
no ip domain-lookup
ip domain-name demo.lab

key chain OSPF
  key 0
    key-string 7 075e731f1a5c4f
vrf context management

interface Ethernet1/1
  description to DC1-L1
  no switchport
  no ip redirects
  ip address 10.2.1.0/31
  ip ospf authentication message-digest
  ip ospf authentication key-chain OSPF
  ip ospf network point-to-point
  ip router ospf UNDERLAY area 0.0.0.0
  no shutdown

interface Ethernet1/2
  description to DC1-L2
  no switchport
  no ip redirects
  ip address 10.2.1.2/31
  ip ospf authentication message-digest
  ip ospf authentication key-chain OSPF
  ip ospf network point-to-point
  ip router ospf UNDERLAY area 0.0.0.0
  no shutdown

interface Ethernet1/3
  description to DC1-L3
  no switchport
  no ip redirects
  ip address 10.2.1.4/31
  ip ospf authentication message-digest
  ip ospf authentication key-chain OSPF
  ip ospf network point-to-point
  ip router ospf UNDERLAY area 0.0.0.0
  no shutdown

interface loopback0
  description RID
  ip address 10.0.0.1/32
  ip ospf network point-to-point
  ip router ospf UNDERLAY area 0.0.0.0

interface loopback1
  description VTEP
  ip address 10.1.0.1/32
  ip ospf network point-to-point
  ip router ospf UNDERLAY area 0.0.0.0
line console
line vty
boot nxos bootflash:/nxos.9.2.2.bin
router ospf UNDERLAY
  router-id 10.0.0.1
```
</details>


<details>
<summary>Конфигурация коммутатора <b>DC1-S2</b>: </summary>

```

hostname DC1-S2
feature ospf

username admin password 5 $5$KTDEXqgq$L8xkct2JY9ERGiltil5uKPeCrvcHgLBTOJkxfhnk6XA  role network-admin
no ip domain-lookup
ip domain-name demo.lab

key chain OSPF
  key 0
    key-string 7 075e731f1a5c4f
vrf context management

interface Ethernet1/1
  description to DC1-L1
  no switchport
  no ip redirects
  ip address 10.2.2.0/31
  ip ospf authentication message-digest
  ip ospf authentication key-chain OSPF
  ip ospf network point-to-point
  ip router ospf UNDERLAY area 0.0.0.0
  no shutdown

interface Ethernet1/2
  description to DC1-L2
  no switchport
  no ip redirects
  ip address 10.2.2.2/31
  ip ospf authentication message-digest
  ip ospf authentication key-chain OSPF
  ip ospf network point-to-point
  ip router ospf UNDERLAY area 0.0.0.0
  no shutdown

interface Ethernet1/3
  description to DC1-L3
  no switchport
  no ip redirects
  ip address 10.2.2.4/31
  ip ospf authentication message-digest
  ip ospf authentication key-chain OSPF
  ip ospf network point-to-point
  ip router ospf UNDERLAY area 0.0.0.0
  no shutdown

interface loopback0
  description RID
  ip address 10.0.0.2/32
  ip ospf network point-to-point
  ip router ospf UNDERLAY area 0.0.0.0

interface loopback1
  description VTEP
  ip address 10.1.0.2/32
  ip ospf network point-to-point
  ip router ospf UNDERLAY area 0.0.0.0
line console
line vty
boot nxos bootflash:/nxos.9.2.2.bin
router ospf UNDERLAY
  router-id 10.0.0.2

```
</details>


<details>
<summary>Конфигурация коммутатора <b>DC1-L1</b>: </summary>

```
hostname DC1-L1
feature ospf

username admin password 5 $5$EpjWybJm$5mpj3jo3pEmSllAwFICLfBi/7uvJmNQ0eheQXqc75kD  role network-admin
no ip domain-lookup
ip domain-name demo.lab

key chain OSPF
  key 0
    key-string 7 075e731f1a5c4f

interface Ethernet1/6
  description to DC1-S1
  no switchport
  no ip redirects
  ip address 10.2.1.1/31
  ip ospf authentication message-digest
  ip ospf authentication key-chain OSPF
  ip ospf network point-to-point
  ip router ospf UNDERLAY area 0.0.0.0
  no shutdown

interface Ethernet1/7
  description to DC1-S2
  no switchport
  no ip redirects
  ip address 10.2.2.1/31
  ip ospf authentication message-digest
  ip ospf authentication key-chain OSPF
  ip ospf network point-to-point
  ip router ospf UNDERLAY area 0.0.0.0
  no shutdown

interface loopback0
  description RID
  ip address 10.0.0.3/32
  ip ospf network point-to-point
  ip router ospf UNDERLAY area 0.0.0.0

interface loopback1
  description VTEP
  ip address 10.1.0.3/32
  ip ospf network point-to-point
  ip router ospf UNDERLAY area 0.0.0.0
line console
line vty
boot nxos bootflash:/nxos.9.2.2.bin
router ospf UNDERLAY
  router-id 10.0.0.3

```
</details>

<details>
<summary>Конфигурация коммутатора <b>DC1-L2</b>: </summary>

```
hostname DC1-L2
feature ospf

username admin password 5 $5$2sk7wEvR$rBRwtTzbHQovBu6NUu5A39ui39SNm.D3airZ3mmIyd/  role network-admin
no ip domain-lookup
ip domain-name demo.lab

key chain OSPF
  key 0
    key-string 7 075e731f1a5c4f

interface Ethernet1/6
  description to DC1-S1
  no switchport
  no ip redirects
  ip address 10.2.1.3/31
  ip ospf authentication message-digest
  ip ospf authentication key-chain OSPF
  ip ospf network point-to-point
  ip router ospf UNDERLAY area 0.0.0.0
  no shutdown

interface Ethernet1/7
  description to DC1-S2
  no switchport
  no ip redirects
  ip address 10.2.2.3/31
  ip ospf authentication message-digest
  ip ospf authentication key-chain OSPF
  ip ospf network point-to-point
  ip router ospf UNDERLAY area 0.0.0.0
  no shutdown

interface loopback0
  description RID
  ip address 10.0.0.4/32
  ip ospf network point-to-point
  ip router ospf UNDERLAY area 0.0.0.0

interface loopback1
  description VTEP
  ip address 10.1.0.4/32
  ip ospf network point-to-point
  ip router ospf UNDERLAY area 0.0.0.0
line console
line vty
boot nxos bootflash:/nxos.9.2.2.bin
router ospf UNDERLAY
  router-id 10.0.0.4
```
</details>

<details>
<summary>Конфигурация коммутатора <b>DC1-L3</b>: </summary>

```
hostname DC1-L3
feature ospf

username admin password 5 $5$tYFbeAcZ$N0t9DOhncMf9ALXrH1TNEC1FhxN6AROcKqpROCJVVa1  role network-admin
no ip domain-lookup
ip domain-name demo.lab

key chain OSPF
  key 0
    key-string 7 075e731f1a5c4f

interface Ethernet1/6
  description to DC1-S1
  no switchport
  no ip redirects
  ip address 10.2.1.5/31
  ip ospf authentication message-digest
  ip ospf authentication key-chain OSPF
  ip ospf network point-to-point
  ip router ospf UNDERLAY area 0.0.0.0
  no shutdown

interface Ethernet1/7
  description to DC1-S2
  no switchport
  no ip redirects
  ip address 10.2.2.5/31
  ip ospf authentication message-digest
  ip ospf authentication key-chain OSPF
  ip ospf network point-to-point
  ip router ospf UNDERLAY area 0.0.0.0
  no shutdown

interface loopback0
  description RID
  ip address 10.0.0.5/32
  ip ospf network point-to-point
  ip router ospf UNDERLAY area 0.0.0.0

interface loopback1
  description VTEP
  ip address 10.1.0.5/32
  ip ospf network point-to-point
  ip router ospf UNDERLAY area 0.0.0.0
line console
line vty
boot nxos bootflash:/nxos.9.2.2.bin
router ospf UNDERLAY
  router-id 10.0.0.5

```
</details>

### Проверка маршрутизации

Проверка OSPF на коммутаторе <b>DC1-L1</b>:
```
DC1-L1# sh ip ospf database
        OSPF Router with ID (10.0.0.3) (Process ID UNDERLAY VRF default)

                Router Link States (Area 0.0.0.0)

Link ID         ADV Router      Age        Seq#       Checksum Link Count
10.0.0.1        10.0.0.1        774        0x8000002b 0xfb21   8
10.0.0.2        10.0.0.2        395        0x80000025 0x9f79   8
10.0.0.3        10.0.0.3        394        0x80000027 0x615a   6
10.0.0.4        10.0.0.4        392        0x80000025 0x278a   6
10.0.0.5        10.0.0.5        396        0x80000025 0xe8bc   6

DC1-L1# sh ip ro
IP Route Table for VRF "default"
'*' denotes best ucast next-hop
'**' denotes best mcast next-hop
'[x/y]' denotes [preference/metric]
'%<string>' in via output denotes VRF <string>

10.0.0.1/32, ubest/mbest: 1/0
    *via 10.2.1.0, Eth1/6, [110/41], 15:01:43, ospf-UNDERLAY, intra
10.0.0.2/32, ubest/mbest: 1/0
    *via 10.2.2.0, Eth1/7, [110/41], 14:47:05, ospf-UNDERLAY, intra
10.0.0.3/32, ubest/mbest: 2/0, attached
    *via 10.0.0.3, Lo0, [0/0], 15:24:43, local
    *via 10.0.0.3, Lo0, [0/0], 15:24:43, direct
10.0.0.4/32, ubest/mbest: 2/0
    *via 10.2.1.0, Eth1/6, [110/81], 14:47:05, ospf-UNDERLAY, intra
    *via 10.2.2.0, Eth1/7, [110/81], 14:47:05, ospf-UNDERLAY, intra
10.0.0.5/32, ubest/mbest: 2/0
    *via 10.2.1.0, Eth1/6, [110/81], 14:47:05, ospf-UNDERLAY, intra
    *via 10.2.2.0, Eth1/7, [110/81], 14:47:05, ospf-UNDERLAY, intra
10.1.0.1/32, ubest/mbest: 1/0
    *via 10.2.1.0, Eth1/6, [110/41], 15:01:43, ospf-UNDERLAY, intra
10.1.0.2/32, ubest/mbest: 1/0
    *via 10.2.2.0, Eth1/7, [110/41], 14:47:05, ospf-UNDERLAY, intra
10.1.0.3/32, ubest/mbest: 2/0, attached
    *via 10.1.0.3, Lo1, [0/0], 15:23:43, local
    *via 10.1.0.3, Lo1, [0/0], 15:23:43, direct
10.1.0.4/32, ubest/mbest: 2/0
    *via 10.2.1.0, Eth1/6, [110/81], 14:47:05, ospf-UNDERLAY, intra
    *via 10.2.2.0, Eth1/7, [110/81], 14:47:05, ospf-UNDERLAY, intra
10.1.0.5/32, ubest/mbest: 2/0
    *via 10.2.1.0, Eth1/6, [110/81], 14:47:05, ospf-UNDERLAY, intra
    *via 10.2.2.0, Eth1/7, [110/81], 14:47:05, ospf-UNDERLAY, intra
10.2.1.0/31, ubest/mbest: 1/0, attached
    *via 10.2.1.1, Eth1/6, [0/0], 15:22:32, direct
10.2.1.1/32, ubest/mbest: 1/0, attached
    *via 10.2.1.1, Eth1/6, [0/0], 15:22:32, local
10.2.1.2/31, ubest/mbest: 1/0
    *via 10.2.1.0, Eth1/6, [110/80], 15:01:37, ospf-UNDERLAY, intra
10.2.1.4/31, ubest/mbest: 1/0
    *via 10.2.1.0, Eth1/6, [110/80], 15:00:21, ospf-UNDERLAY, intra
10.2.2.0/31, ubest/mbest: 1/0, attached
    *via 10.2.2.1, Eth1/7, [0/0], 15:22:08, direct
10.2.2.1/32, ubest/mbest: 1/0, attached
    *via 10.2.2.1, Eth1/7, [0/0], 15:22:08, local
10.2.2.2/31, ubest/mbest: 1/0
    *via 10.2.2.0, Eth1/7, [110/80], 14:47:05, ospf-UNDERLAY, intra
10.2.2.4/31, ubest/mbest: 1/0
    *via 10.2.2.0, Eth1/7, [110/80], 14:47:05, ospf-UNDERLAY, intra

```
Проверка OSPF на коммутаторе<b> DC1-L2</b>:
```
DC1-L2# sh ip ospf database
        OSPF Router with ID (10.0.0.4) (Process ID UNDERLAY VRF default)

                Router Link States (Area 0.0.0.0)

Link ID         ADV Router      Age        Seq#       Checksum Link Count
10.0.0.1        10.0.0.1        1345       0x8000002c 0xf922   8
10.0.0.2        10.0.0.2        967        0x80000026 0x9d7a   8
10.0.0.3        10.0.0.3        967        0x80000028 0x5f5b   6
10.0.0.4        10.0.0.4        961        0x80000026 0x258b   6
10.0.0.5        10.0.0.5        968        0x80000026 0xe6bd   6

DC1-L2# sh ip ro
IP Route Table for VRF "default"
'*' denotes best ucast next-hop
'**' denotes best mcast next-hop
'[x/y]' denotes [preference/metric]
'%<string>' in via output denotes VRF <string>

10.0.0.1/32, ubest/mbest: 1/0
    *via 10.2.1.2, Eth1/6, [110/41], 15:40:41, ospf-UNDERLAY, intra
10.0.0.2/32, ubest/mbest: 1/0
    *via 10.2.2.2, Eth1/7, [110/41], 15:26:24, ospf-UNDERLAY, intra
10.0.0.3/32, ubest/mbest: 2/0
    *via 10.2.1.2, Eth1/6, [110/81], 15:26:24, ospf-UNDERLAY, intra
    *via 10.2.2.2, Eth1/7, [110/81], 15:26:24, ospf-UNDERLAY, intra
10.0.0.4/32, ubest/mbest: 2/0, attached
    *via 10.0.0.4, Lo0, [0/0], 15:46:14, local
    *via 10.0.0.4, Lo0, [0/0], 15:46:14, direct
10.0.0.5/32, ubest/mbest: 2/0
    *via 10.2.1.2, Eth1/6, [110/81], 15:26:24, ospf-UNDERLAY, intra
    *via 10.2.2.2, Eth1/7, [110/81], 15:26:24, ospf-UNDERLAY, intra
10.1.0.1/32, ubest/mbest: 1/0
    *via 10.2.1.2, Eth1/6, [110/41], 15:40:41, ospf-UNDERLAY, intra
10.1.0.2/32, ubest/mbest: 1/0
    *via 10.2.2.2, Eth1/7, [110/41], 15:26:24, ospf-UNDERLAY, intra
10.1.0.3/32, ubest/mbest: 2/0
    *via 10.2.1.2, Eth1/6, [110/81], 15:26:24, ospf-UNDERLAY, intra
    *via 10.2.2.2, Eth1/7, [110/81], 15:26:24, ospf-UNDERLAY, intra
10.1.0.4/32, ubest/mbest: 2/0, attached
    *via 10.1.0.4, Lo1, [0/0], 15:46:13, local
    *via 10.1.0.4, Lo1, [0/0], 15:46:13, direct
10.1.0.5/32, ubest/mbest: 2/0
    *via 10.2.1.2, Eth1/6, [110/81], 15:26:24, ospf-UNDERLAY, intra
    *via 10.2.2.2, Eth1/7, [110/81], 15:26:24, ospf-UNDERLAY, intra
10.2.1.0/31, ubest/mbest: 1/0
    *via 10.2.1.2, Eth1/6, [110/80], 15:40:41, ospf-UNDERLAY, intra
10.2.1.2/31, ubest/mbest: 1/0, attached
    *via 10.2.1.3, Eth1/6, [0/0], 15:44:52, direct
10.2.1.3/32, ubest/mbest: 1/0, attached
    *via 10.2.1.3, Eth1/6, [0/0], 15:44:52, local
10.2.1.4/31, ubest/mbest: 1/0
    *via 10.2.1.2, Eth1/6, [110/80], 15:39:40, ospf-UNDERLAY, intra
10.2.2.0/31, ubest/mbest: 1/0
    *via 10.2.2.2, Eth1/7, [110/80], 15:26:24, ospf-UNDERLAY, intra
10.2.2.2/31, ubest/mbest: 1/0, attached
    *via 10.2.2.3, Eth1/7, [0/0], 15:44:44, direct
10.2.2.3/32, ubest/mbest: 1/0, attached
    *via 10.2.2.3, Eth1/7, [0/0], 15:44:44, local
10.2.2.4/31, ubest/mbest: 1/0
    *via 10.2.2.2, Eth1/7, [110/80], 15:26:24, ospf-UNDERLAY, intra
```

Проверка OSPF на коммутаторе <b>DC1-L3</b>:
```
DC1-L3# sh ip ospf database
        OSPF Router with ID (10.0.0.5) (Process ID UNDERLAY VRF default)

                Router Link States (Area 0.0.0.0)

Link ID         ADV Router      Age        Seq#       Checksum Link Count
10.0.0.1        10.0.0.1        1386       0x8000002c 0xf922   8
10.0.0.2        10.0.0.2        1008       0x80000026 0x9d7a   8
10.0.0.3        10.0.0.3        1008       0x80000028 0x5f5b   6
10.0.0.4        10.0.0.4        1004       0x80000026 0x258b   6
10.0.0.5        10.0.0.5        1007       0x80000026 0xe6bd   6

DC1-L3# sh ip ro
IP Route Table for VRF "default"
'*' denotes best ucast next-hop
'**' denotes best mcast next-hop
'[x/y]' denotes [preference/metric]
'%<string>' in via output denotes VRF <string>

10.0.0.1/32, ubest/mbest: 1/0
    *via 10.2.1.4, Eth1/6, [110/41], 15:33:22, ospf-UNDERLAY, intra
10.0.0.2/32, ubest/mbest: 1/0
    *via 10.2.2.4, Eth1/7, [110/41], 15:27:05, ospf-UNDERLAY, intra
10.0.0.3/32, ubest/mbest: 2/0
    *via 10.2.1.4, Eth1/6, [110/81], 15:27:02, ospf-UNDERLAY, intra
    *via 10.2.2.4, Eth1/7, [110/81], 15:27:02, ospf-UNDERLAY, intra
10.0.0.4/32, ubest/mbest: 2/0
    *via 10.2.1.4, Eth1/6, [110/81], 15:27:02, ospf-UNDERLAY, intra
    *via 10.2.2.4, Eth1/7, [110/81], 15:27:02, ospf-UNDERLAY, intra
10.0.0.5/32, ubest/mbest: 2/0, attached
    *via 10.0.0.5, Lo0, [0/0], 15:33:50, local
    *via 10.0.0.5, Lo0, [0/0], 15:33:50, direct
10.1.0.1/32, ubest/mbest: 1/0
    *via 10.2.1.4, Eth1/6, [110/41], 15:33:22, ospf-UNDERLAY, intra
10.1.0.2/32, ubest/mbest: 1/0
    *via 10.2.2.4, Eth1/7, [110/41], 15:27:05, ospf-UNDERLAY, intra
10.1.0.3/32, ubest/mbest: 2/0
    *via 10.2.1.4, Eth1/6, [110/81], 15:27:02, ospf-UNDERLAY, intra
    *via 10.2.2.4, Eth1/7, [110/81], 15:27:02, ospf-UNDERLAY, intra
10.1.0.4/32, ubest/mbest: 2/0
    *via 10.2.1.4, Eth1/6, [110/81], 15:27:02, ospf-UNDERLAY, intra
    *via 10.2.2.4, Eth1/7, [110/81], 15:27:02, ospf-UNDERLAY, intra
10.1.0.5/32, ubest/mbest: 2/0, attached
    *via 10.1.0.5, Lo1, [0/0], 15:33:50, local
    *via 10.1.0.5, Lo1, [0/0], 15:33:50, direct
10.2.1.0/31, ubest/mbest: 1/0
    *via 10.2.1.4, Eth1/6, [110/80], 15:33:22, ospf-UNDERLAY, intra
10.2.1.2/31, ubest/mbest: 1/0
    *via 10.2.1.4, Eth1/6, [110/80], 15:33:22, ospf-UNDERLAY, intra
10.2.1.4/31, ubest/mbest: 1/0, attached
    *via 10.2.1.5, Eth1/6, [0/0], 15:33:35, direct
10.2.1.5/32, ubest/mbest: 1/0, attached
    *via 10.2.1.5, Eth1/6, [0/0], 15:33:35, local
10.2.2.0/31, ubest/mbest: 1/0
    *via 10.2.2.4, Eth1/7, [110/80], 15:27:05, ospf-UNDERLAY, intra
10.2.2.2/31, ubest/mbest: 1/0
    *via 10.2.2.4, Eth1/7, [110/80], 15:27:05, ospf-UNDERLAY, intra
10.2.2.4/31, ubest/mbest: 1/0, attached
    *via 10.2.2.5, Eth1/7, [0/0], 15:33:34, direct
10.2.2.5/32, ubest/mbest: 1/0, attached
    *via 10.2.2.5, Eth1/7, [0/0], 15:33:34, local
```
