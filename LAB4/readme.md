## Домашнее задание
Построение Underlay сети (BGP)

Цель:
- Сконфигурировать динамическую маршрутизацию на базе протокола BGP;
- Проверить динамическую маршрутизацию и доступность Loopback0 на коммутатора LEAF.

## Схема сети

![schemeBGP.png](schemeBGP.png)

## Выполнение домашней работы в EVE-NG

### Настройка коммутаторов

<details>
<summary>Конфигурация коммутатора <b>DC1-S1</b>: </summary>

```
hostname DC1-S1
feature bgp

interface Ethernet1/1
  description to DC1-L1
  no switchport
  no ip redirects
  ip address 10.2.1.0/31
  no shutdown

interface Ethernet1/2
  description to DC1-L2
  no switchport
  no ip redirects
  ip address 10.2.1.2/31
  no shutdown

interface Ethernet1/3
  description to DC1-L3
  no switchport
  no ip redirects
  ip address 10.2.1.4/31
  no shutdown

interface loopback0
  description RID
  ip address 10.0.0.1/32

interface loopback1
  description VTEP
  ip address 10.1.0.1/32

router bgp 65000
  router-id 10.0.0.1
  bestpath as-path multipath-relax
  address-family ipv4 unicast
    maximum-paths 64
  neighbor 10.2.1.1
    remote-as 65001
    description DC1-L1
    password 7 1454372F2F572F2F27
    timers 3 9
    address-family ipv4 unicast
  neighbor 10.2.1.3
    remote-as 65002
    description DC1-L2
    password 7 1454372F2F572F2F27
    timers 3 9
    address-family ipv4 unicast
  neighbor 10.2.1.5
    remote-as 65003
    description DC1-L3
    password 7 1454372F2F572F2F27
    timers 3 9
    address-family ipv4 unicast

```
</details>


<details>
<summary>Конфигурация коммутатора <b>DC1-S2</b>: </summary>

```
hostname DC1-S2
feature bgp

interface Ethernet1/1
  description to DC1-L1
  no switchport
  no ip redirects
  ip address 10.2.2.0/31
  no shutdown

interface Ethernet1/2
  description to DC1-L2
  no switchport
  no ip redirects
  ip address 10.2.2.2/31
  no shutdown

interface Ethernet1/3
  description to DC1-L3
  no switchport
  no ip redirects
  ip address 10.2.2.4/31
  no shutdown

interface loopback0
  description RID
  ip address 10.0.0.2/32

interface loopback1
  description VTEP
  ip address 10.1.0.2/32

router bgp 65000
  router-id 10.0.0.2
  bestpath as-path multipath-relax
  address-family ipv4 unicast
    maximum-paths 64
  neighbor 10.2.2.1
    remote-as 65001
    description DC1-L1
    password 7 1454372F2F572F2F27
    timers 3 9
    address-family ipv4 unicast
  neighbor 10.2.2.3
    remote-as 65002
    description DC1-L2
    password 7 1454372F2F572F2F27
    timers 3 9
    address-family ipv4 unicast
  neighbor 10.2.2.5
    remote-as 65003
    description DC1-L3
    password 7 1454372F2F572F2F27
    timers 3 9
    address-family ipv4 unicast

```
</details>


<details>
<summary>Конфигурация коммутатора <b>DC1-L1</b>: </summary>

```
hostname DC1-L1
feature  bgp

route-map REDISTR-CONNECTED permit 10
  match interface loopback0
route-map REDISTR-CONNECTED permit 20
  match interface loopback1

interface Ethernet1/6
  description to DC1-S1
  no switchport
  no ip redirects
  ip address 10.2.1.1/31
  no shutdown

interface Ethernet1/7
  description to DC1-S2
  no switchport
  no ip redirects
  ip address 10.2.2.1/31
  no shutdown

interface loopback0
  description RID
  ip address 10.0.0.3/32

interface loopback1
  description VTEP
  ip address 10.1.0.3/32

router bgp 65001
  router-id 10.0.0.3
  bestpath as-path multipath-relax
  reconnect-interval 12
  address-family ipv4 unicast
    redistribute direct route-map REDISTR-CONNECTED
    maximum-paths 64
  template peer SPINE
    remote-as 65000
    password 7 1454372F2F572F2F27
    timers 3 9
    address-family ipv4 unicast
  neighbor 10.0.0.4
    remote-as 65002
    description peer to DC1-L2
    password 7 1454372F2F572F2F27
    update-source loopback0
    ebgp-multihop 2
    address-family ipv4 unicast
    address-family l2vpn evpn
  neighbor 10.0.0.5
    remote-as 65003
    description peer to DC1-L3
    password 7 1454372F2F572F2F27
    update-source loopback0
    ebgp-multihop 2
    address-family ipv4 unicast
    address-family l2vpn evpn
  neighbor 10.2.1.0
    inherit peer SPINE
    description peer to DC1-S1
  neighbor 10.2.2.0
    inherit peer SPINE
    description peer to DC1-S2

```
</details>

<details>
<summary>Конфигурация коммутатора <b>DC1-L2</b>: </summary>

```
hostname DC1-L2
feature bgp

route-map REDISTR-CONNECTED permit 10
  match interface loopback0
route-map REDISTR-CONNECTED permit 20
  match interface loopback1


interface Ethernet1/6
  description to DC1-S1
  no switchport
  no ip redirects
  ip address 10.2.1.3/31
  no shutdown

interface Ethernet1/7
  description to DC1-S2
  no switchport
  no ip redirects
  ip address 10.2.2.3/31
  no shutdown

  interface loopback0
    description RID
    ip address 10.0.0.4/32
  
  interface loopback1
    description VTEP
    ip address 10.1.0.4/32

  router bgp 65002
    router-id 10.0.0.4
    bestpath as-path multipath-relax
    reconnect-interval 12
    address-family ipv4 unicast
      redistribute direct route-map REDISTR-CONNECTED
      maximum-paths 64
    template peer SPINE
      remote-as 65000
      password 7 1454372F2F572F2F27
      timers 3 9
      address-family ipv4 unicast
    neighbor 10.0.0.3
      remote-as 65001
      description peer to DC1-L1
      password 7 1454372F2F572F2F27
      update-source loopback0
      ebgp-multihop 2
      address-family ipv4 unicast
      address-family l2vpn evpn
    neighbor 10.0.0.5
      remote-as 65003
      description peer to DC1-L3
      password 7 1454372F2F572F2F27
      update-source loopback0
      ebgp-multihop 2
      address-family ipv4 unicast
      address-family l2vpn evpn
    neighbor 10.2.1.2
      inherit peer SPINE
      description peer to DC1-S1
    neighbor 10.2.2.2
      inherit peer SPINE
    description peer to DC1-S2

```
</details>

<details>
<summary>Конфигурация коммутатора <b>DC1-L3</b>: </summary>

```
hostname DC1-L3
feature bgp

route-map REDISTR-CONNECTED permit 10
  match interface loopback0
route-map REDISTR-CONNECTED permit 20
  match interface loopback1


interface Ethernet1/6
  description to DC1-S1
  no switchport
  no ip redirects
  ip address 10.2.1.5/31
  no shutdown

interface Ethernet1/7
  description to DC1-S2
  no switchport
  no ip redirects
  ip address 10.2.2.5/31
  no shutdown
  
interface loopback0
  description RID
  ip address 10.0.0.5/32
 
interface loopback1
  description VTEP
  ip address 10.1.0.5/32

router bgp 65003
  router-id 10.0.0.5
  bestpath as-path multipath-relax
  reconnect-interval 12
  address-family ipv4 unicast
    redistribute direct route-map REDISTR-CONNECTED
    maximum-paths 64
  template peer SPINE
    remote-as 65000
    password 7 1454372F2F572F2F27
    timers 3 9
    address-family ipv4 unicast
  neighbor 10.0.0.3
    remote-as 65001
    description peer to DC1-L1
    password 7 1454372F2F572F2F27
    update-source loopback0
    ebgp-multihop 2
    address-family ipv4 unicast
    address-family l2vpn evpn
  neighbor 10.0.0.4
    remote-as 65002
    description peer to DC1-L2
    password 7 1454372F2F572F2F27
    update-source loopback0
    ebgp-multihop 2
    address-family ipv4 unicast
    address-family l2vpn evpn
  neighbor 10.2.1.4
    inherit peer SPINE
    description peer to DC1-S1
  neighbor 10.2.2.4
    inherit peer SPINE
    description peer to DC1-S2


```
</details>

### Проверка маршрутизации

Проверка BGP на коммутаторе <b>DC1-L1</b>:
```

DC1-L1# sh ip bgp sum
BGP summary information for VRF default, address family IPv4 Unicast
BGP router identifier 10.0.0.3, local AS number 65001
BGP table version is 30532, IPv4 Unicast config peers 4, capable peers 4
6 network entries and 18 paths using 2808 bytes of memory
BGP attribute entries [7/1148], BGP AS path entries [6/56]
BGP community entries [0/0], BGP clusterlist entries [0/0]

Neighbor        V    AS MsgRcvd MsgSent   TblVer  InQ OutQ Up/Down  State/PfxRcd
10.0.0.4        4 65002    6501    2442    30532    0    0 01:49:50 4
10.0.0.5        4 65003    3924    1237    30532    0    0 00:39:10 4
10.2.1.0        4 65000   85184   87756    30532    0    0 02:10:47 4
10.2.2.0        4 65000   84933   87434    30532    0    0 01:56:21 4


DC1-L1# sh ip ro bgp
IP Route Table for VRF "default"
'*' denotes best ucast next-hop
'**' denotes best mcast next-hop
'[x/y]' denotes [preference/metric]
'%<string>' in via output denotes VRF <string>

10.0.0.4/32, ubest/mbest: 2/0
    *via 10.2.1.0, [20/0], 00:00:01, bgp-65001, external, tag 65000
    *via 10.2.2.0, [20/0], 00:00:01, bgp-65001, external, tag 65000
10.0.0.5/32, ubest/mbest: 2/0
    *via 10.2.1.0, [20/0], 00:00:01, bgp-65001, external, tag 65000
    *via 10.2.2.0, [20/0], 00:00:01, bgp-65001, external, tag 65000
10.1.0.4/32, ubest/mbest: 2/0
    *via 10.2.1.0, [20/0], 00:00:01, bgp-65001, external, tag 65000
    *via 10.2.2.0, [20/0], 00:00:01, bgp-65001, external, tag 65000
10.1.0.5/32, ubest/mbest: 2/0
    *via 10.2.1.0, [20/0], 00:00:01, bgp-65001, external, tag 65000
    *via 10.2.2.0, [20/0], 00:00:01, bgp-65001, external, tag 65000

```
Проверка BGP на коммутаторе<b> DC1-L2</b>:
```
DC1-L2# sh ip bgp sum
BGP summary information for VRF default, address family IPv4 Unicast
BGP router identifier 10.0.0.4, local AS number 65002
BGP table version is 27036, IPv4 Unicast config peers 4, capable peers 4
6 network entries and 18 paths using 2808 bytes of memory
BGP attribute entries [7/1148], BGP AS path entries [6/60]
BGP community entries [0/0], BGP clusterlist entries [0/0]

Neighbor        V    AS MsgRcvd MsgSent   TblVer  InQ OutQ Up/Down  State/PfxRcd
10.0.0.3        4 65001    6359    2127    27036    0    0 01:51:26 4
10.0.0.5        4 65003    3221    1719    27036    0    0 00:02:41 4
10.2.1.2        4 65000   85039   87469    27036    0    0 02:03:19 4
10.2.2.2        4 65000   85149   87545    27036    0    0 01:58:02 4

DC1-L2# sh ip ro bgp
IP Route Table for VRF "default"
'*' denotes best ucast next-hop
'**' denotes best mcast next-hop
'[x/y]' denotes [preference/metric]
'%<string>' in via output denotes VRF <string>

10.0.0.3/32, ubest/mbest: 2/0
    *via 10.2.1.2, [20/0], 00:00:03, bgp-65002, external, tag 65000
    *via 10.2.2.2, [20/0], 00:00:03, bgp-65002, external, tag 65000
10.0.0.5/32, ubest/mbest: 2/0
    *via 10.2.1.2, [20/0], 00:00:03, bgp-65002, external, tag 65000
    *via 10.2.2.2, [20/0], 00:00:03, bgp-65002, external, tag 65000
10.1.0.3/32, ubest/mbest: 2/0
    *via 10.2.1.2, [20/0], 00:00:03, bgp-65002, external, tag 65000
    *via 10.2.2.2, [20/0], 00:00:03, bgp-65002, external, tag 65000
10.1.0.5/32, ubest/mbest: 2/0
    *via 10.2.1.2, [20/0], 00:00:03, bgp-65002, external, tag 65000
    *via 10.2.2.2, [20/0], 00:00:03, bgp-65002, external, tag 65000

```

Проверка BGP на коммутаторе <b>DC1-L3</b>:
```
DC1-L3# sh ip bgp sum
BGP summary information for VRF default, address family IPv4 Unicast
BGP router identifier 10.0.0.5, local AS number 65003
BGP table version is 18720, IPv4 Unicast config peers 4, capable peers 4
6 network entries and 18 paths using 2808 bytes of memory
BGP attribute entries [7/1148], BGP AS path entries [6/60]
BGP community entries [0/0], BGP clusterlist entries [0/0]

Neighbor        V    AS MsgRcvd MsgSent   TblVer  InQ OutQ Up/Down  State/PfxRcd
10.0.0.3        4 65001    3831    1352    18720    0    0 00:42:15 4
10.0.0.4        4 65002    3110    1349    18720    0    0 00:00:49 4
10.2.1.4        4 65000   85067   86721    18720    0    0 02:04:04 4
10.2.2.4        4 65000   85183   86858    18720    0    0 01:59:14 4

DC1-L3# sh ip ro bgp
IP Route Table for VRF "default"
'*' denotes best ucast next-hop
'**' denotes best mcast next-hop
'[x/y]' denotes [preference/metric]
'%<string>' in via output denotes VRF <string>

10.0.0.3/32, ubest/mbest: 2/0
    *via 10.2.1.4, [20/0], 00:00:03, bgp-65003, external, tag 65000
    *via 10.2.2.4, [20/0], 00:00:03, bgp-65003, external, tag 65000
10.0.0.4/32, ubest/mbest: 2/0
    *via 10.2.1.4, [20/0], 00:00:03, bgp-65003, external, tag 65000
    *via 10.2.2.4, [20/0], 00:00:03, bgp-65003, external, tag 65000
10.1.0.3/32, ubest/mbest: 2/0
    *via 10.2.1.4, [20/0], 00:00:03, bgp-65003, external, tag 65000
    *via 10.2.2.4, [20/0], 00:00:03, bgp-65003, external, tag 65000
10.1.0.4/32, ubest/mbest: 2/0
    *via 10.2.1.4, [20/0], 00:00:03, bgp-65003, external, tag 65000
    *via 10.2.2.4, [20/0], 00:00:03, bgp-65003, external, tag 65000

```

Проверка BGP на коммутаторе <b>DC1-S1</b>:
```
DC1-S1# sh ip bgp sum
BGP summary information for VRF default, address family IPv4 Unicast
BGP router identifier 10.0.0.1, local AS number 65000
BGP table version is 18914, IPv4 Unicast config peers 3, capable peers 3
6 network entries and 12 paths using 2064 bytes of memory
BGP attribute entries [6/984], BGP AS path entries [6/48]
BGP community entries [0/0], BGP clusterlist entries [0/0]

Neighbor        V    AS MsgRcvd MsgSent   TblVer  InQ OutQ Up/Down  State/PfxRcd
10.2.1.1        4 65001   91388   85267    18914    0    0 02:15:36 2
10.2.1.3        4 65002   91057   85095    18914    0    0 02:06:31 4
10.2.1.5        4 65003   89201   85088    18914    0    0 02:05:47 4

DC1-S1# sh ip ro bgp
IP Route Table for VRF "default"
'*' denotes best ucast next-hop
'**' denotes best mcast next-hop
'[x/y]' denotes [preference/metric]
'%<string>' in via output denotes VRF <string>

10.0.0.3/32, ubest/mbest: 1/0
    *via 10.2.1.1, [20/0], 02:15:43, bgp-65000, external, tag 65001
10.0.0.4/32, ubest/mbest: 1/0
    *via 10.2.1.3, [20/0], 02:06:39, bgp-65000, external, tag 65002
10.0.0.5/32, ubest/mbest: 1/0
    *via 10.2.1.5, [20/0], 02:05:55, bgp-65000, external, tag 65003
10.1.0.3/32, ubest/mbest: 1/0
    *via 10.2.1.1, [20/0], 02:15:43, bgp-65000, external, tag 65001
10.1.0.4/32, ubest/mbest: 1/0
    *via 10.2.1.3, [20/0], 02:06:39, bgp-65000, external, tag 65002
10.1.0.5/32, ubest/mbest: 1/0
    *via 10.2.1.5, [20/0], 02:05:55, bgp-65000, external, tag 65003

```

Проверка BGP на коммутаторе <b>DC1-S2</b>:
```
DC1-S2# sh ip bgp sum
BGP summary information for VRF default, address family IPv4 Unicast
BGP router identifier 10.0.0.2, local AS number 65000
BGP table version is 18952, IPv4 Unicast config peers 3, capable peers 3
6 network entries and 12 paths using 2064 bytes of memory
BGP attribute entries [6/984], BGP AS path entries [6/48]
BGP community entries [0/0], BGP clusterlist entries [0/0]

Neighbor        V    AS MsgRcvd MsgSent   TblVer  InQ OutQ Up/Down  State/PfxRcd
10.2.2.1        4 65001   91030   85036    18952    0    0 02:02:08 2
10.2.2.3        4 65002   91223   85219    18952    0    0 02:02:13 2
10.2.2.5        4 65003   89393   85225    18952    0    0 02:01:56 4
DC1-S2#
DC1-S2# sh ip ro bgp
IP Route Table for VRF "default"
'*' denotes best ucast next-hop
'**' denotes best mcast next-hop
'[x/y]' denotes [preference/metric]
'%<string>' in via output denotes VRF <string>

10.0.0.3/32, ubest/mbest: 1/0
    *via 10.2.2.1, [20/0], 02:02:14, bgp-65000, external, tag 65001
10.0.0.4/32, ubest/mbest: 1/0
    *via 10.2.2.3, [20/0], 02:02:19, bgp-65000, external, tag 65002
10.0.0.5/32, ubest/mbest: 1/0
    *via 10.2.2.5, [20/0], 02:02:02, bgp-65000, external, tag 65003
10.1.0.3/32, ubest/mbest: 1/0
    *via 10.2.2.1, [20/0], 02:02:14, bgp-65000, external, tag 65001
10.1.0.4/32, ubest/mbest: 1/0
    *via 10.2.2.3, [20/0], 02:02:19, bgp-65000, external, tag 65002
10.1.0.5/32, ubest/mbest: 1/0
    *via 10.2.2.5, [20/0], 02:02:02, bgp-65000, external, tag 65003

```