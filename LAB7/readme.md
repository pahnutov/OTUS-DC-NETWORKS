## Домашнее задание
<b>VxLAN VPC </b>

Цель:
- Настроить vPC пару свичей DC1-L1 и DC1-L2;
- подключить Server1 и Server2 аггрегированными каналами к vPC паре.
- Проверить связность между клиентами vPC пары и отдельным LEAF DC1-L3 в одном VLANе.

<br>


## Схема физической коммутации стенда.

![connections.png](connections.png)

## Логическая схема настройки vPC пары.

![vpc.png](vpc.png)

<br>

## Выполнение домашней работы в EVE-NG

В качестве overlay сети используется конфигурация <b>LAB5. VXLAN L2.</b><br>

### Настройка коммутаторов

Ниже приведены команды Указаны команды, используемые только в рамках этой лабы. Остальные настройки - без изменений.

<details>
<summary>Конфигурация коммутатора <b>DC1-L1</b>: </summary>

```
nv overlay evpn
feature bgp
feature interface-vlan
feature vn-segment-vlan-based
feature nv overlay

vlan 10
  name Vlan10
  vn-segment 10010

interface Vlan10
  description TEST
  no shutdown
  ip address 192.168.10.1/24
  fabric forwarding mode anycast-gateway

interface nve1
  no shutdown
  host-reachability protocol bgp
  source-interface loopback1
  member vni 10010
    ingress-replication protocol bgp
      
interface Ethernet1/1
  description Server1
  switchport access vlan 10
  
  router bgp 65001
    router-id 10.0.0.3
    bestpath as-path multipath-relax
    reconnect-interval 12
    address-family ipv4 unicast
      redistribute direct route-map REDISTR-CONNECTED
      maximum-paths 64
    address-family l2vpn evpn
      retain route-target all
    template peer SPINE
      remote-as 65000
      password 7 1454372F2F572F2F27
      timers 3 9
      address-family ipv4 unicast
    template peer SPINE-L2VPN
      remote-as 65000
      password 7 1454372F2F572F2F27
      update-source loopback0
      ebgp-multihop 2
      address-family l2vpn evpn
        send-community
        send-community extended
    neighbor 10.0.0.1
      inherit peer SPINE-L2VPN
    neighbor 10.0.0.2
      inherit peer SPINE-L2VPN
    neighbor 10.2.1.0
      inherit peer SPINE
      description peer to DC1-S1
    neighbor 10.2.2.0
      inherit peer SPINE
      description peer to DC1-S2
  evpn
    vni 10010 l2
      rd 65001:10
      route-target import 10:10010
      route-target export 10:10010

```
</details>

<details>
<summary>Конфигурация коммутатора <b>DC1-L2</b>: </summary>

```
hostname DC1-L2
nv overlay evpn
feature bgp
feature interface-vlan
feature vn-segment-vlan-based
feature nv overlay

vlan 20
  name PROD
  vn-segment 10020

interface Vlan20
  description PROD
  no shutdown
  ip address 192.168.20.1/24
  fabric forwarding mode anycast-gateway

interface nve1
  no shutdown
  host-reachability protocol bgp
  source-interface loopback1
  member vni 10020
    ingress-replication protocol bgp

interface Ethernet1/1
  description Server02
  switchport access vlan 20

router bgp 65002
  router-id 10.0.0.4
  bestpath as-path multipath-relax
  reconnect-interval 12
  address-family ipv4 unicast
    redistribute direct route-map REDISTR-CONNECTED
    maximum-paths 64
  address-family l2vpn evpn
    retain route-target all
  template peer SPINE
    remote-as 65000
    password 7 1454372F2F572F2F27
    timers 3 9
    address-family ipv4 unicast
  template peer SPINE-L2VPN
    remote-as 65000
    password 7 1454372F2F572F2F27
    update-source loopback0
    ebgp-multihop 2
    address-family l2vpn evpn
      send-community
      send-community extended
  neighbor 10.0.0.1
    inherit peer SPINE-L2VPN
  neighbor 10.0.0.2
    inherit peer SPINE-L2VPN
  neighbor 10.2.1.2
    inherit peer SPINE
    description peer to DC1-S1
  neighbor 10.2.2.2
    inherit peer SPINE
    description peer to DC1-S2
evpn
  vni 10020 l2
    rd 65002:20
    route-target import 20:10020
    route-target export 20:10020

```
</details>

<details>
<summary>Конфигурация коммутатора <b>DC1-L3</b>: </summary>

```
hostname DC1-L3

nv overlay evpn
feature bgp
feature interface-vlan
feature vn-segment-vlan-based
feature nv overlay

vlan 10
  name Vlan10
  vn-segment 10010
vlan 20
  name Vlan20
  vn-segment 10020

interface Vlan10
  description TEST
  no shutdown
  ip address 192.168.10.1/24
  fabric forwarding mode anycast-gateway

interface Vlan20
  description PROD
  no shutdown
  ip address 192.168.20.1/24
  fabric forwarding mode anycast-gateway

interface nve1
  no shutdown
  host-reachability protocol bgp
  source-interface loopback1
  member vni 10010
    ingress-replication protocol bgp
  member vni 10020
    ingress-replication protocol bgp

interface Ethernet1/1
  description Server3
  switchport access vlan 10

interface Ethernet1/2
  description Server4
  switchport access vlan 20

router bgp 65003
  router-id 10.0.0.5
  bestpath as-path multipath-relax
  reconnect-interval 12
  address-family ipv4 unicast
    redistribute direct route-map REDISTR-CONNECTED
    maximum-paths 64
  address-family l2vpn evpn
    retain route-target all
  template peer SPINE
    remote-as 65000
    password 7 1454372F2F572F2F27
    timers 3 9
    address-family ipv4 unicast
  template peer SPINE-L2VPN
    remote-as 65000
    password 7 1454372F2F572F2F27
    update-source loopback0
    ebgp-multihop 2
    address-family l2vpn evpn
      send-community
      send-community extended
  neighbor 10.0.0.1
    inherit peer SPINE-L2VPN
  neighbor 10.0.0.2
    inherit peer SPINE-L2VPN
  neighbor 10.2.1.4
    inherit peer SPINE
    description peer to DC1-S1
  neighbor 10.2.2.4
    inherit peer SPINE
    description peer to DC1-S2
evpn
  vni 10010 l2
    rd 65003:10
    route-target import 10:10010
    route-target export 10:10010
  vni 10020 l2
    rd 65003:20
    route-target import 20:10020
    route-target export 20:10020
```
</details>

### Проверка маршрутизации Overlay в AFI L2VPN EVPN

Проверка протокола EVPN на коммутаторе <b>DC1-S1</b>:
```
DC1-S1# sh bgp l2 e sum
BGP summary information for VRF default, address family L2VPN EVPN
BGP router identifier 10.0.0.1, local AS number 65000
BGP table version is 1307, L2VPN EVPN config peers 3, capable peers 3
12 network entries and 12 paths using 2928 bytes of memory
BGP attribute entries [8/1376], BGP AS path entries [3/18]
BGP community entries [0/0], BGP clusterlist entries [0/0]

Neighbor        V    AS MsgRcvd MsgSent   TblVer  InQ OutQ Up/Down  State/PfxRcd
10.0.0.3        4 65001     104     100     1307    0    0 01:25:22 3
10.0.0.4        4 65002      87      87     1307    0    0 01:17:36 3
10.0.0.5        4 65003     108      99     1307    0    0 01:32:20 6

```

Проверка протокола EVPN на коммутаторе <b>DC1-S2</b>:
```
DC1-S2# sh bgp l2 e sum
BGP summary information for VRF default, address family L2VPN EVPN
BGP router identifier 10.0.0.2, local AS number 65000
BGP table version is 19, L2VPN EVPN config peers 3, capable peers 3
12 network entries and 12 paths using 2928 bytes of memory
BGP attribute entries [8/1376], BGP AS path entries [3/18]
BGP community entries [0/0], BGP clusterlist entries [0/0]

Neighbor        V    AS MsgRcvd MsgSent   TblVer  InQ OutQ Up/Down  State/PfxRcd
10.0.0.3        4 65001      53      52       19    0    0 00:45:57 3
10.0.0.4        4 65002      49      48       19    0    0 00:41:42 3
10.0.0.5        4 65003      56      51       19    0    0 00:45:52 6

```

### Проверка связности по L2 

Проверка протокола VxLAN на коммутаторе <b>DC1-L1</b>:
```
DC1-L1(config)# sh nve peer
Interface Peer-IP                                 State LearnType Uptime   Router-Mac
--------- --------------------------------------  ----- --------- -------- -----------------
nve1      10.1.0.5                                Up    CP        01:03:25 n/a


DC1-L1(config)# sh nve vni
Codes: CP - Control Plane        DP - Data Plane
       UC - Unconfigured         SA - Suppress ARP
       SU - Suppress Unknown Unicast
       Xconn - Crossconnect
       MS-IR - Multisite Ingress Replication

Interface VNI      Multicast-group   State Mode Type [BD/VRF]      Flags
--------- -------- ----------------- ----- ---- ------------------ -----
nve1      10010    UnicastBGP        Up    CP   L2 [10]

DC1-L1(config)# sh bgp l2 e sum
BGP summary information for VRF default, address family L2VPN EVPN
BGP router identifier 10.0.0.3, local AS number 65001
BGP table version is 762, L2VPN EVPN config peers 2, capable peers 2
15 network entries and 24 paths using 4380 bytes of memory
BGP attribute entries [9/1548], BGP AS path entries [2/20]
BGP community entries [0/0], BGP clusterlist entries [0/0]

Neighbor        V    AS MsgRcvd MsgSent   TblVer  InQ OutQ Up/Down  State/PfxRcd
10.0.0.1        4 65000      82      70      762    0    0 01:05:13 9
10.0.0.2        4 65000      38      31      762    0    0 00:25:12 9

DC1-L1(config)# sh mac addr
Legend:
        * - primary entry, G - Gateway MAC, (R) - Routed MAC, O - Overlay MAC
        age - seconds since last seen,+ - primary entry using vPC Peer-Link,
        (T) - True, (F) - False, C - ControlPlane MAC, ~ - vsan
   VLAN     MAC Address      Type      age     Secure NTFY Ports
---------+-----------------+--------+---------+------+----+------------------
*   10     aabb.cc00.2000   dynamic  0         F      F    Eth1/1
C   10     aabb.cc00.3000   dynamic  0         F      F    nve1(10.1.0.5)
G    -     1234.1234.1234   static   -         F      F    sup-eth1(R)
G    -     500c.0000.1b08   static   -         F      F    sup-eth1(R)
G   10     500c.0000.1b08   static   -         F      F    sup-eth1(R)

```
Проверка протокола VxLAN на коммутаторе <b> DC1-L2</b>:
```

DC1-L2(config)# sh nve peer
Interface Peer-IP                                 State LearnType Uptime   Router-Mac
--------- --------------------------------------  ----- --------- -------- -----------------
nve1      10.1.0.5                                Up    CP        00:36:30 n/a

DC1-L2(config)# sh nve vni
Codes: CP - Control Plane        DP - Data Plane
       UC - Unconfigured         SA - Suppress ARP
       SU - Suppress Unknown Unicast
       Xconn - Crossconnect
       MS-IR - Multisite Ingress Replication

Interface VNI      Multicast-group   State Mode Type [BD/VRF]      Flags
--------- -------- ----------------- ----- ---- ------------------ -----
nve1      10020    UnicastBGP        Up    CP   L2 [20]

DC1-L2(config)# sh bgp l2 e sum
BGP summary information for VRF default, address family L2VPN EVPN
BGP router identifier 10.0.0.4, local AS number 65002
BGP table version is 59, L2VPN EVPN config peers 2, capable peers 2
15 network entries and 24 paths using 4380 bytes of memory
BGP attribute entries [9/1548], BGP AS path entries [2/20]
BGP community entries [0/0], BGP clusterlist entries [0/0]

Neighbor        V    AS MsgRcvd MsgSent   TblVer  InQ OutQ Up/Down  State/PfxRcd
10.0.0.1        4 65000      85      66       59    0    0 00:59:14 9
10.0.0.2        4 65000      35      28       59    0    0 00:22:42 9
DC1-L2(config)# sh mac addr
Legend:
        * - primary entry, G - Gateway MAC, (R) - Routed MAC, O - Overlay MAC
        age - seconds since last seen,+ - primary entry using vPC Peer-Link,
        (T) - True, (F) - False, C - ControlPlane MAC, ~ - vsan
   VLAN     MAC Address      Type      age     Secure NTFY Ports
---------+-----------------+--------+---------+------+----+------------------
C   20     aabb.cc00.4000   dynamic  0         F      F    nve1(10.1.0.5)
*   20     aabb.cc00.5000   dynamic  0         F      F    Eth1/1
G    -     1234.1234.1234   static   -         F      F    sup-eth1(R)
G    -     500d.0000.1b08   static   -         F      F    sup-eth1(R)
G   20     500d.0000.1b08   static   -         F      F    sup-eth1(R)

```

Проверка протокола VxLAN на коммутаторе <b>DC1-L3</b>:
```
DC1-L3# sh nve peer
Interface Peer-IP                                 State LearnType Uptime   Router-Mac
--------- --------------------------------------  ----- --------- -------- -----------------
nve1      10.1.0.3                                Up    CP        01:08:13 n/a
nve1      10.1.0.4                                Up    CP        00:38:13 n/a

DC1-L3# sh nve vni
Codes: CP - Control Plane        DP - Data Plane
       UC - Unconfigured         SA - Suppress ARP
       SU - Suppress Unknown Unicast
       Xconn - Crossconnect
       MS-IR - Multisite Ingress Replication

Interface VNI      Multicast-group   State Mode Type [BD/VRF]      Flags
--------- -------- ----------------- ----- ---- ------------------ -----
nve1      10010    UnicastBGP        Up    CP   L2 [10]
nve1      10020    UnicastBGP        Up    CP   L2 [20]

DC1-L3# sh bgp l2 e sum
BGP summary information for VRF default, address family L2VPN EVPN
BGP router identifier 10.0.0.5, local AS number 65003
BGP table version is 4538, L2VPN EVPN config peers 2, capable peers 2
18 network entries and 24 paths using 4392 bytes of memory
BGP attribute entries [10/1720], BGP AS path entries [2/20]
BGP community entries [0/0], BGP clusterlist entries [0/0]

Neighbor        V    AS MsgRcvd MsgSent   TblVer  InQ OutQ Up/Down  State/PfxRcd
10.0.0.1        4 65000     238     336     4538    0    0 01:15:30 6
10.0.0.2        4 65000      38      35     4538    0    0 00:28:25 6
DC1-L3#
DC1-L3#
DC1-L3# sh mac addr
Legend:
        * - primary entry, G - Gateway MAC, (R) - Routed MAC, O - Overlay MAC
        age - seconds since last seen,+ - primary entry using vPC Peer-Link,
        (T) - True, (F) - False, C - ControlPlane MAC, ~ - vsan
   VLAN     MAC Address      Type      age     Secure NTFY Ports
---------+-----------------+--------+---------+------+----+------------------
C   10     aabb.cc00.2000   dynamic  0         F      F    nve1(10.1.0.3)
*   10     aabb.cc00.3000   dynamic  0         F      F    Eth1/1
*   20     aabb.cc00.4000   dynamic  0         F      F    Eth1/2
C   20     aabb.cc00.5000   dynamic  0         F      F    nve1(10.1.0.4)
G    -     1234.1234.1234   static   -         F      F    sup-eth1(R)
G    -     500e.0000.1b08   static   -         F      F    sup-eth1(R)
G   10     500e.0000.1b08   static   -         F      F    sup-eth1(R)
G   20     500e.0000.1b08   static   -         F      F    sup-eth1(R)

```

Проверка доступности хостов <b>Server1 < - > Server3</b>:
```
Server1#sh ip int br
Interface              IP-Address      OK? Method Status                Protocol
Ethernet0/0            192.168.10.100  YES NVRAM  up                    up
Ethernet0/1            unassigned      YES unset  up                    up
Ethernet0/2            unassigned      YES unset  up                    up
Ethernet0/3            unassigned      YES unset  up                    up
Server1#
Server1#ping 192.168.10.200
Type escape sequence to abort.
Sending 5, 100-byte ICMP Echos to 192.168.10.200, timeout is 2 seconds:
!!!!!
Success rate is 100 percent (5/5), round-trip min/avg/max = 11/25/61 ms
Server1#

Server3#sh ip int br
Interface              IP-Address      OK? Method Status                Protocol
Ethernet0/0            192.168.10.200  YES manual up                    up
Ethernet0/1            unassigned      YES unset  up                    up
Ethernet0/2            unassigned      YES unset  up                    up
Ethernet0/3            unassigned      YES unset  up                    up
Server3#ping 192.168.10.100
Type escape sequence to abort.
Sending 5, 100-byte ICMP Echos to 192.168.10.100, timeout is 2 seconds:
!!!!!
Success rate is 100 percent (5/5), round-trip min/avg/max = 10/32/57 ms

```

Проверка доступности хостов <b>Server2 < - > Server4</b>:
```
Server2#sh ip int br
Interface              IP-Address      OK? Method Status                Protocol
Ethernet0/0            192.168.20.10   YES NVRAM  up                    up
Ethernet0/1            unassigned      YES unset  up                    up
Ethernet0/2            unassigned      YES unset  up                    up
Ethernet0/3            unassigned      YES unset  up                    up
Server2#ping 192.168.20.20
Type escape sequence to abort.
Sending 5, 100-byte ICMP Echos to 192.168.20.20, timeout is 2 seconds:
!!!!!
Success rate is 100 percent (5/5), round-trip min/avg/max = 8/23/50 ms

Server4#sh ip int br
Interface              IP-Address      OK? Method Status                Protocol
Ethernet0/0            192.168.20.20   YES manual up                    up
Ethernet0/1            unassigned      YES unset  up                    up
Ethernet0/2            unassigned      YES unset  up                    up
Ethernet0/3            unassigned      YES unset  up                    up
Server4#ping 192.168.20.10
Type escape sequence to abort.
Sending 5, 100-byte ICMP Echos to 192.168.20.10, timeout is 2 seconds:
!!!!!
Success rate is 100 percent (5/5), round-trip min/avg/max = 8/24/80 ms


```
