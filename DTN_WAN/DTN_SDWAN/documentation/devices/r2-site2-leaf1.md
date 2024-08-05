# r2-site2-leaf1

## Table of Contents

- [Management](#management)
  - [Management Interfaces](#management-interfaces)
  - [DNS Domain](#dns-domain)
  - [IP Name Servers](#ip-name-servers)
  - [NTP](#ntp)
  - [Management API HTTP](#management-api-http)
- [Authentication](#authentication)
  - [Local Users](#local-users)
  - [AAA Authorization](#aaa-authorization)
- [Monitoring](#monitoring)
  - [TerminAttr Daemon](#terminattr-daemon)
- [Spanning Tree](#spanning-tree)
  - [Spanning Tree Summary](#spanning-tree-summary)
  - [Spanning Tree Device Configuration](#spanning-tree-device-configuration)
- [Internal VLAN Allocation Policy](#internal-vlan-allocation-policy)
  - [Internal VLAN Allocation Policy Summary](#internal-vlan-allocation-policy-summary)
  - [Internal VLAN Allocation Policy Device Configuration](#internal-vlan-allocation-policy-device-configuration)
- [VLANs](#vlans)
  - [VLANs Summary](#vlans-summary)
  - [VLANs Device Configuration](#vlans-device-configuration)
- [Interfaces](#interfaces)
  - [Ethernet Interfaces](#ethernet-interfaces)
  - [Loopback Interfaces](#loopback-interfaces)
  - [VLAN Interfaces](#vlan-interfaces)
  - [VXLAN Interface](#vxlan-interface)
- [Routing](#routing)
  - [Service Routing Protocols Model](#service-routing-protocols-model)
  - [IP Routing](#ip-routing)
  - [IPv6 Routing](#ipv6-routing)
  - [Static Routes](#static-routes)
  - [Router BGP](#router-bgp)
- [BFD](#bfd)
  - [Router BFD](#router-bfd)
- [Multicast](#multicast)
  - [IP IGMP Snooping](#ip-igmp-snooping)
- [Filters](#filters)
  - [Prefix-lists](#prefix-lists)
  - [Route-maps](#route-maps)
- [VRF Instances](#vrf-instances)
  - [VRF Instances Summary](#vrf-instances-summary)
  - [VRF Instances Device Configuration](#vrf-instances-device-configuration)

## Management

### Management Interfaces

#### Management Interfaces Summary

##### IPv4

| Management Interface | Description | Type | VRF | IP Address | Gateway |
| -------------------- | ----------- | ---- | --- | ---------- | ------- |
| Management1 | oob_management | oob | MGMT | 172.28.129.213/17 | 172.28.128.1 |

##### IPv6

| Management Interface | Description | Type | VRF | IPv6 Address | IPv6 Gateway |
| -------------------- | ----------- | ---- | --- | ------------ | ------------ |
| Management1 | oob_management | oob | MGMT | - | - |

#### Management Interfaces Device Configuration

```eos
!
interface Management1
   description oob_management
   no shutdown
   vrf MGMT
   ip address 172.28.129.213/17
```

### DNS Domain

DNS domain: fun.aristanetworks.com

#### DNS Domain Device Configuration

```eos
dns domain fun.aristanetworks.com
!
```

### IP Name Servers

#### IP Name Servers Summary

| Name Server | VRF | Priority |
| ----------- | --- | -------- |
| 10.14.0.1 | MGMT | - |

#### IP Name Servers Device Configuration

```eos
ip name-server vrf MGMT 10.14.0.1
```

### NTP

#### NTP Summary

##### NTP Local Interface

| Interface | VRF |
| --------- | --- |
| Management1 | MGMT |

##### NTP Servers

| Server | VRF | Preferred | Burst | iBurst | Version | Min Poll | Max Poll | Local-interface | Key |
| ------ | --- | --------- | ----- | ------ | ------- | -------- | -------- | --------------- | --- |
| 1.pool.ntp.org | MGMT | True | - | True | - | - | - | - | - |

#### NTP Device Configuration

```eos
!
ntp local-interface vrf MGMT Management1
ntp server vrf MGMT 1.pool.ntp.org prefer iburst
```

### Management API HTTP

#### Management API HTTP Summary

| HTTP | HTTPS | Default Services |
| ---- | ----- | ---------------- |
| False | True | - |

#### Management API VRF Access

| VRF Name | IPv4 ACL | IPv6 ACL |
| -------- | -------- | -------- |
| MGMT | - | - |

#### Management API HTTP Device Configuration

```eos
!
management api http-commands
   protocol https
   no shutdown
   !
   vrf MGMT
      no shutdown
```

## Authentication

### Local Users

#### Local Users Summary

| User | Privilege | Role | Disabled | Shell |
| ---- | --------- | ---- | -------- | ----- |
| admin | 15 | network-admin | False | - |
| cvpadmin | 15 | network-admin | False | - |

#### Local Users Device Configuration

```eos
!
username admin privilege 15 role network-admin secret sha512 <removed>
username cvpadmin privilege 15 role network-admin secret sha512 <removed>
```

### AAA Authorization

#### AAA Authorization Summary

| Type | User Stores |
| ---- | ----------- |
| Exec | local |

Authorization for configuration commands is disabled.

#### AAA Authorization Device Configuration

```eos
aaa authorization exec default local
!
```

## Monitoring

### TerminAttr Daemon

#### TerminAttr Daemon Summary

| CV Compression | CloudVision Servers | VRF | Authentication | Smash Excludes | Ingest Exclude | Bypass AAA |
| -------------- | ------------------- | --- | -------------- | -------------- | -------------- | ---------- |
| gzip | apiserver.cv-staging.corp.arista.io:443 | MGMT | token-secure,/mnt/flash/token | ale,flexCounter,hardware,kni,pulse,strata | /Sysdb/cell/1/agent,/Sysdb/cell/2/agent | True |

#### TerminAttr Daemon Device Configuration

```eos
!
daemon TerminAttr
   exec /usr/bin/TerminAttr -cvaddr=apiserver.cv-staging.corp.arista.io:443 -cvauth=token-secure,/mnt/flash/token -cvvrf=MGMT -disableaaa -smashexcludes=ale,flexCounter,hardware,kni,pulse,strata -ingestexclude=/Sysdb/cell/1/agent,/Sysdb/cell/2/agent -taillogs
   no shutdown
```

## Spanning Tree

### Spanning Tree Summary

STP mode: **mstp**

#### MSTP Instance and Priority

| Instance(s) | Priority |
| -------- | -------- |
| 0 | 16384 |

### Spanning Tree Device Configuration

```eos
!
spanning-tree mode mstp
spanning-tree mst 0 priority 16384
```

## Internal VLAN Allocation Policy

### Internal VLAN Allocation Policy Summary

| Policy Allocation | Range Beginning | Range Ending |
| ------------------| --------------- | ------------ |
| ascending | 1006 | 1199 |

### Internal VLAN Allocation Policy Device Configuration

```eos
!
vlan internal order ascending range 1006 1199
```

## VLANs

### VLANs Summary

| VLAN ID | Name | Trunk Groups |
| ------- | ---- | ------------ |
| 2 | customer2 | - |
| 3 | customer3 | - |
| 4 | customer4 | - |
| 5 | customer5 | - |
| 6 | customer6 | - |
| 254 | customer1 | - |

### VLANs Device Configuration

```eos
!
vlan 2
   name customer2
!
vlan 3
   name customer3
!
vlan 4
   name customer4
!
vlan 5
   name customer5
!
vlan 6
   name customer6
!
vlan 254
   name customer1
```

## Interfaces

### Ethernet Interfaces

#### Ethernet Interfaces Summary

##### L2

| Interface | Description | Mode | VLANs | Native VLAN | Trunk Group | Channel-Group |
| --------- | ----------- | ---- | ----- | ----------- | ----------- | ------------- |
| Ethernet49/1 |  test-host | trunk | 1-255 | - | - | - |

*Inherited from Port-Channel Interface

##### Encapsulation Dot1q Interfaces

| Interface | Description | Type | Vlan ID | Dot1q VLAN Tag |
| --------- | ----------- | -----| ------- | -------------- |
| Ethernet47.2 | P2P_LINK_TO_R2-SITE2-WAN1_Ethernet2.2_vrf_customer2 | l3dot1q | - | 2 |
| Ethernet47.3 | P2P_LINK_TO_R2-SITE2-WAN1_Ethernet2.3_vrf_customer3 | l3dot1q | - | 3 |
| Ethernet47.4 | P2P_LINK_TO_R2-SITE2-WAN1_Ethernet2.4_vrf_customer4 | l3dot1q | - | 4 |
| Ethernet47.5 | P2P_LINK_TO_R2-SITE2-WAN1_Ethernet2.5_vrf_customer5 | l3dot1q | - | 5 |
| Ethernet47.6 | P2P_LINK_TO_R2-SITE2-WAN1_Ethernet2.6_vrf_customer6 | l3dot1q | - | 6 |
| Ethernet47.254 | P2P_LINK_TO_R2-SITE2-WAN1_Ethernet2.254_vrf_customer1 | l3dot1q | - | 254 |
| Ethernet48.2 | P2P_LINK_TO_R2-SITE2-WAN2_Ethernet2.2_vrf_customer2 | l3dot1q | - | 2 |
| Ethernet48.3 | P2P_LINK_TO_R2-SITE2-WAN2_Ethernet2.3_vrf_customer3 | l3dot1q | - | 3 |
| Ethernet48.4 | P2P_LINK_TO_R2-SITE2-WAN2_Ethernet2.4_vrf_customer4 | l3dot1q | - | 4 |
| Ethernet48.5 | P2P_LINK_TO_R2-SITE2-WAN2_Ethernet2.5_vrf_customer5 | l3dot1q | - | 5 |
| Ethernet48.6 | P2P_LINK_TO_R2-SITE2-WAN2_Ethernet2.6_vrf_customer6 | l3dot1q | - | 6 |
| Ethernet48.254 | P2P_LINK_TO_R2-SITE2-WAN2_Ethernet2.254_vrf_customer1 | l3dot1q | - | 254 |

##### IPv4

| Interface | Description | Type | Channel Group | IP Address | VRF |  MTU | Shutdown | ACL In | ACL Out |
| --------- | ----------- | -----| ------------- | ---------- | ----| ---- | -------- | ------ | ------- |
| Ethernet47 | P2P_LINK_TO_R2-SITE2-WAN1_Ethernet2 | routed | - | 10.2.2.128/31 | default | 9214 | False | - | - |
| Ethernet47.2 | P2P_LINK_TO_R2-SITE2-WAN1_Ethernet2.2_vrf_customer2 | l3dot1q | - | 10.2.2.128/31 | customer2 | 9214 | False | - | - |
| Ethernet47.3 | P2P_LINK_TO_R2-SITE2-WAN1_Ethernet2.3_vrf_customer3 | l3dot1q | - | 10.2.2.128/31 | customer3 | 9214 | False | - | - |
| Ethernet47.4 | P2P_LINK_TO_R2-SITE2-WAN1_Ethernet2.4_vrf_customer4 | l3dot1q | - | 10.2.2.128/31 | customer4 | 9214 | False | - | - |
| Ethernet47.5 | P2P_LINK_TO_R2-SITE2-WAN1_Ethernet2.5_vrf_customer5 | l3dot1q | - | 10.2.2.128/31 | customer5 | 9214 | False | - | - |
| Ethernet47.6 | P2P_LINK_TO_R2-SITE2-WAN1_Ethernet2.6_vrf_customer6 | l3dot1q | - | 10.2.2.128/31 | customer6 | 9214 | False | - | - |
| Ethernet47.254 | P2P_LINK_TO_R2-SITE2-WAN1_Ethernet2.254_vrf_customer1 | l3dot1q | - | 10.2.2.128/31 | customer1 | 9214 | False | - | - |
| Ethernet48 | P2P_LINK_TO_R2-SITE2-WAN2_Ethernet2 | routed | - | 10.2.2.130/31 | default | 9214 | False | - | - |
| Ethernet48.2 | P2P_LINK_TO_R2-SITE2-WAN2_Ethernet2.2_vrf_customer2 | l3dot1q | - | 10.2.2.130/31 | customer2 | 9214 | False | - | - |
| Ethernet48.3 | P2P_LINK_TO_R2-SITE2-WAN2_Ethernet2.3_vrf_customer3 | l3dot1q | - | 10.2.2.130/31 | customer3 | 9214 | False | - | - |
| Ethernet48.4 | P2P_LINK_TO_R2-SITE2-WAN2_Ethernet2.4_vrf_customer4 | l3dot1q | - | 10.2.2.130/31 | customer4 | 9214 | False | - | - |
| Ethernet48.5 | P2P_LINK_TO_R2-SITE2-WAN2_Ethernet2.5_vrf_customer5 | l3dot1q | - | 10.2.2.130/31 | customer5 | 9214 | False | - | - |
| Ethernet48.6 | P2P_LINK_TO_R2-SITE2-WAN2_Ethernet2.6_vrf_customer6 | l3dot1q | - | 10.2.2.130/31 | customer6 | 9214 | False | - | - |
| Ethernet48.254 | P2P_LINK_TO_R2-SITE2-WAN2_Ethernet2.254_vrf_customer1 | l3dot1q | - | 10.2.2.130/31 | customer1 | 9214 | False | - | - |

#### Ethernet Interfaces Device Configuration

```eos
!
interface Ethernet47
   description P2P_LINK_TO_R2-SITE2-WAN1_Ethernet2
   no shutdown
   mtu 9214
   no switchport
   ip address 10.2.2.128/31
!
interface Ethernet47.2
   description P2P_LINK_TO_R2-SITE2-WAN1_Ethernet2.2_vrf_customer2
   no shutdown
   mtu 9214
   encapsulation dot1q vlan 2
   vrf customer2
   ip address 10.2.2.128/31
!
interface Ethernet47.3
   description P2P_LINK_TO_R2-SITE2-WAN1_Ethernet2.3_vrf_customer3
   no shutdown
   mtu 9214
   encapsulation dot1q vlan 3
   vrf customer3
   ip address 10.2.2.128/31
!
interface Ethernet47.4
   description P2P_LINK_TO_R2-SITE2-WAN1_Ethernet2.4_vrf_customer4
   no shutdown
   mtu 9214
   encapsulation dot1q vlan 4
   vrf customer4
   ip address 10.2.2.128/31
!
interface Ethernet47.5
   description P2P_LINK_TO_R2-SITE2-WAN1_Ethernet2.5_vrf_customer5
   no shutdown
   mtu 9214
   encapsulation dot1q vlan 5
   vrf customer5
   ip address 10.2.2.128/31
!
interface Ethernet47.6
   description P2P_LINK_TO_R2-SITE2-WAN1_Ethernet2.6_vrf_customer6
   no shutdown
   mtu 9214
   encapsulation dot1q vlan 6
   vrf customer6
   ip address 10.2.2.128/31
!
interface Ethernet47.254
   description P2P_LINK_TO_R2-SITE2-WAN1_Ethernet2.254_vrf_customer1
   no shutdown
   mtu 9214
   encapsulation dot1q vlan 254
   vrf customer1
   ip address 10.2.2.128/31
!
interface Ethernet48
   description P2P_LINK_TO_R2-SITE2-WAN2_Ethernet2
   no shutdown
   mtu 9214
   no switchport
   ip address 10.2.2.130/31
!
interface Ethernet48.2
   description P2P_LINK_TO_R2-SITE2-WAN2_Ethernet2.2_vrf_customer2
   no shutdown
   mtu 9214
   encapsulation dot1q vlan 2
   vrf customer2
   ip address 10.2.2.130/31
!
interface Ethernet48.3
   description P2P_LINK_TO_R2-SITE2-WAN2_Ethernet2.3_vrf_customer3
   no shutdown
   mtu 9214
   encapsulation dot1q vlan 3
   vrf customer3
   ip address 10.2.2.130/31
!
interface Ethernet48.4
   description P2P_LINK_TO_R2-SITE2-WAN2_Ethernet2.4_vrf_customer4
   no shutdown
   mtu 9214
   encapsulation dot1q vlan 4
   vrf customer4
   ip address 10.2.2.130/31
!
interface Ethernet48.5
   description P2P_LINK_TO_R2-SITE2-WAN2_Ethernet2.5_vrf_customer5
   no shutdown
   mtu 9214
   encapsulation dot1q vlan 5
   vrf customer5
   ip address 10.2.2.130/31
!
interface Ethernet48.6
   description P2P_LINK_TO_R2-SITE2-WAN2_Ethernet2.6_vrf_customer6
   no shutdown
   mtu 9214
   encapsulation dot1q vlan 6
   vrf customer6
   ip address 10.2.2.130/31
!
interface Ethernet48.254
   description P2P_LINK_TO_R2-SITE2-WAN2_Ethernet2.254_vrf_customer1
   no shutdown
   mtu 9214
   encapsulation dot1q vlan 254
   vrf customer1
   ip address 10.2.2.130/31
!
interface Ethernet49/1
   description test-host
   no shutdown
   switchport trunk allowed vlan 1-255
   switchport mode trunk
   switchport
```

### Loopback Interfaces

#### Loopback Interfaces Summary

##### IPv4

| Interface | Description | VRF | IP Address |
| --------- | ----------- | --- | ---------- |
| Loopback0 | EVPN_Overlay_Peering | default | 10.2.2.3/32 |
| Loopback1 | VTEP_VXLAN_Tunnel_Source | default | 10.2.2.67/32 |

##### IPv6

| Interface | Description | VRF | IPv6 Address |
| --------- | ----------- | --- | ------------ |
| Loopback0 | EVPN_Overlay_Peering | default | - |
| Loopback1 | VTEP_VXLAN_Tunnel_Source | default | - |

#### Loopback Interfaces Device Configuration

```eos
!
interface Loopback0
   description EVPN_Overlay_Peering
   no shutdown
   ip address 10.2.2.3/32
!
interface Loopback1
   description VTEP_VXLAN_Tunnel_Source
   no shutdown
   ip address 10.2.2.67/32
```

### VLAN Interfaces

#### VLAN Interfaces Summary

| Interface | Description | VRF |  MTU | Shutdown |
| --------- | ----------- | --- | ---- | -------- |
| Vlan2 | customer2 | customer2 | - | False |
| Vlan3 | customer3 | customer3 | - | False |
| Vlan4 | customer4 | customer4 | - | False |
| Vlan5 | customer5 | customer5 | - | False |
| Vlan6 | customer6 | customer6 | - | False |
| Vlan254 | customer1 | customer1 | - | False |

##### IPv4

| Interface | VRF | IP Address | IP Address Virtual | IP Router Virtual Address | VRRP | ACL In | ACL Out |
| --------- | --- | ---------- | ------------------ | ------------------------- | ---- | ------ | ------- |
| Vlan2 |  customer2  |  10.22.2.1/24  |  -  |  -  |  -  |  -  |  -  |
| Vlan3 |  customer3  |  10.22.3.1/24  |  -  |  -  |  -  |  -  |  -  |
| Vlan4 |  customer4  |  10.22.4.1/24  |  -  |  -  |  -  |  -  |  -  |
| Vlan5 |  customer5  |  10.22.5.1/24  |  -  |  -  |  -  |  -  |  -  |
| Vlan6 |  customer6  |  10.22.6.1/24  |  -  |  -  |  -  |  -  |  -  |
| Vlan254 |  customer1  |  10.22.1.1/24  |  -  |  -  |  -  |  -  |  -  |

#### VLAN Interfaces Device Configuration

```eos
!
interface Vlan2
   description customer2
   no shutdown
   vrf customer2
   ip address 10.22.2.1/24
!
interface Vlan3
   description customer3
   no shutdown
   vrf customer3
   ip address 10.22.3.1/24
!
interface Vlan4
   description customer4
   no shutdown
   vrf customer4
   ip address 10.22.4.1/24
!
interface Vlan5
   description customer5
   no shutdown
   vrf customer5
   ip address 10.22.5.1/24
!
interface Vlan6
   description customer6
   no shutdown
   vrf customer6
   ip address 10.22.6.1/24
!
interface Vlan254
   description customer1
   no shutdown
   vrf customer1
   ip address 10.22.1.1/24
```

### VXLAN Interface

#### VXLAN Interface Summary

| Setting | Value |
| ------- | ----- |
| Source Interface | Loopback1 |
| UDP port | 4789 |

##### VLAN to VNI, Flood List and Multicast Group Mappings

| VLAN | VNI | Flood List | Multicast Group |
| ---- | --- | ---------- | --------------- |
| 2 | 10002 | - | - |
| 3 | 10003 | - | - |
| 4 | 10004 | - | - |
| 5 | 10005 | - | - |
| 6 | 10006 | - | - |
| 254 | 10254 | - | - |

##### VRF to VNI and Multicast Group Mappings

| VRF | VNI | Multicast Group |
| ---- | --- | --------------- |
| customer1 | 254 | - |
| customer2 | 2 | - |
| customer3 | 3 | - |
| customer4 | 4 | - |
| customer5 | 5 | - |
| customer6 | 6 | - |

#### VXLAN Interface Device Configuration

```eos
!
interface Vxlan1
   description r2-site2-leaf1_VTEP
   vxlan source-interface Loopback1
   vxlan udp-port 4789
   vxlan vlan 2 vni 10002
   vxlan vlan 3 vni 10003
   vxlan vlan 4 vni 10004
   vxlan vlan 5 vni 10005
   vxlan vlan 6 vni 10006
   vxlan vlan 254 vni 10254
   vxlan vrf customer1 vni 254
   vxlan vrf customer2 vni 2
   vxlan vrf customer3 vni 3
   vxlan vrf customer4 vni 4
   vxlan vrf customer5 vni 5
   vxlan vrf customer6 vni 6
```

## Routing

### Service Routing Protocols Model

Multi agent routing protocol model enabled

```eos
!
service routing protocols model multi-agent
```

### IP Routing

#### IP Routing Summary

| VRF | Routing Enabled |
| --- | --------------- |
| default | True |
| customer1 | True |
| customer2 | True |
| customer3 | True |
| customer4 | True |
| customer5 | True |
| customer6 | True |
| MGMT | False |

#### IP Routing Device Configuration

```eos
!
ip routing
ip routing vrf customer1
ip routing vrf customer2
ip routing vrf customer3
ip routing vrf customer4
ip routing vrf customer5
ip routing vrf customer6
no ip routing vrf MGMT
```

### IPv6 Routing

#### IPv6 Routing Summary

| VRF | Routing Enabled |
| --- | --------------- |
| default | False |
| customer1 | false |
| customer2 | false |
| customer3 | false |
| customer4 | false |
| customer5 | false |
| customer6 | false |
| MGMT | false |

### Static Routes

#### Static Routes Summary

| VRF | Destination Prefix | Next Hop IP | Exit interface | Administrative Distance | Tag | Route Name | Metric |
| --- | ------------------ | ----------- | -------------- | ----------------------- | --- | ---------- | ------ |
| MGMT | 0.0.0.0/0 | 172.28.128.1 | - | 1 | - | - | - |

#### Static Routes Device Configuration

```eos
!
ip route vrf MGMT 0.0.0.0/0 172.28.128.1
```

### Router BGP

ASN Notation: asplain

#### Router BGP Summary

| BGP AS | Router ID |
| ------ | --------- |
| 65022 | 10.2.2.3 |

| BGP Tuning |
| ---------- |
| no bgp default ipv4-unicast |
| maximum-paths 4 ecmp 4 |

#### Router BGP Peer Groups

##### EVPN-OVERLAY-PEERS

| Settings | Value |
| -------- | ----- |
| Address Family | evpn |
| Source | Loopback0 |
| BFD | True |
| Ebgp multihop | 3 |
| Send community | all |
| Maximum routes | 0 (no limit) |

##### IPv4-UNDERLAY-PEERS

| Settings | Value |
| -------- | ----- |
| Address Family | ipv4 |
| Send community | all |
| Maximum routes | 12000 |

#### BGP Neighbors

| Neighbor | Remote AS | VRF | Shutdown | Send-community | Maximum-routes | Allowas-in | BFD | RIB Pre-Policy Retain | Route-Reflector Client | Passive | TTL Max Hops |
| -------- | --------- | --- | -------- | -------------- | -------------- | ---------- | --- | --------------------- | ---------------------- | ------- | ------------ |
| 10.2.2.129 | 65000 | default | - | Inherited from peer group IPv4-UNDERLAY-PEERS | Inherited from peer group IPv4-UNDERLAY-PEERS | - | - | - | - | - | - |
| 10.2.2.131 | 65000 | default | - | Inherited from peer group IPv4-UNDERLAY-PEERS | Inherited from peer group IPv4-UNDERLAY-PEERS | - | - | - | - | - | - |
| 10.2.2.129 | 65000 | customer1 | - | Inherited from peer group IPv4-UNDERLAY-PEERS | Inherited from peer group IPv4-UNDERLAY-PEERS | - | - | - | - | - | - |
| 10.2.2.131 | 65000 | customer1 | - | Inherited from peer group IPv4-UNDERLAY-PEERS | Inherited from peer group IPv4-UNDERLAY-PEERS | - | - | - | - | - | - |
| 10.2.2.129 | 65000 | customer2 | - | Inherited from peer group IPv4-UNDERLAY-PEERS | Inherited from peer group IPv4-UNDERLAY-PEERS | - | - | - | - | - | - |
| 10.2.2.131 | 65000 | customer2 | - | Inherited from peer group IPv4-UNDERLAY-PEERS | Inherited from peer group IPv4-UNDERLAY-PEERS | - | - | - | - | - | - |
| 10.2.2.129 | 65000 | customer3 | - | Inherited from peer group IPv4-UNDERLAY-PEERS | Inherited from peer group IPv4-UNDERLAY-PEERS | - | - | - | - | - | - |
| 10.2.2.131 | 65000 | customer3 | - | Inherited from peer group IPv4-UNDERLAY-PEERS | Inherited from peer group IPv4-UNDERLAY-PEERS | - | - | - | - | - | - |
| 10.2.2.129 | 65000 | customer4 | - | Inherited from peer group IPv4-UNDERLAY-PEERS | Inherited from peer group IPv4-UNDERLAY-PEERS | - | - | - | - | - | - |
| 10.2.2.131 | 65000 | customer4 | - | Inherited from peer group IPv4-UNDERLAY-PEERS | Inherited from peer group IPv4-UNDERLAY-PEERS | - | - | - | - | - | - |
| 10.2.2.129 | 65000 | customer5 | - | Inherited from peer group IPv4-UNDERLAY-PEERS | Inherited from peer group IPv4-UNDERLAY-PEERS | - | - | - | - | - | - |
| 10.2.2.131 | 65000 | customer5 | - | Inherited from peer group IPv4-UNDERLAY-PEERS | Inherited from peer group IPv4-UNDERLAY-PEERS | - | - | - | - | - | - |
| 10.2.2.129 | 65000 | customer6 | - | Inherited from peer group IPv4-UNDERLAY-PEERS | Inherited from peer group IPv4-UNDERLAY-PEERS | - | - | - | - | - | - |
| 10.2.2.131 | 65000 | customer6 | - | Inherited from peer group IPv4-UNDERLAY-PEERS | Inherited from peer group IPv4-UNDERLAY-PEERS | - | - | - | - | - | - |

#### Router BGP EVPN Address Family

##### EVPN Peer Groups

| Peer Group | Activate | Encapsulation |
| ---------- | -------- | ------------- |
| EVPN-OVERLAY-PEERS | True | default |

#### Router BGP VLANs

| VLAN | Route-Distinguisher | Both Route-Target | Import Route Target | Export Route-Target | Redistribute |
| ---- | ------------------- | ----------------- | ------------------- | ------------------- | ------------ |
| 2 | 10.2.2.3:10002 | 10002:10002 | - | - | learned |
| 3 | 10.2.2.3:10003 | 10003:10003 | - | - | learned |
| 4 | 10.2.2.3:10004 | 10004:10004 | - | - | learned |
| 5 | 10.2.2.3:10005 | 10005:10005 | - | - | learned |
| 6 | 10.2.2.3:10006 | 10006:10006 | - | - | learned |
| 254 | 10.2.2.3:10254 | 10254:10254 | - | - | learned |

#### Router BGP VRFs

| VRF | Route-Distinguisher | Redistribute |
| --- | ------------------- | ------------ |
| customer1 | 10.2.2.3:254 | connected |
| customer2 | 10.2.2.3:2 | connected |
| customer3 | 10.2.2.3:3 | connected |
| customer4 | 10.2.2.3:4 | connected |
| customer5 | 10.2.2.3:5 | connected |
| customer6 | 10.2.2.3:6 | connected |

#### Router BGP Device Configuration

```eos
!
router bgp 65022
   router-id 10.2.2.3
   maximum-paths 4 ecmp 4
   no bgp default ipv4-unicast
   neighbor EVPN-OVERLAY-PEERS peer group
   neighbor EVPN-OVERLAY-PEERS update-source Loopback0
   neighbor EVPN-OVERLAY-PEERS bfd
   neighbor EVPN-OVERLAY-PEERS ebgp-multihop 3
   neighbor EVPN-OVERLAY-PEERS send-community
   neighbor EVPN-OVERLAY-PEERS maximum-routes 0
   neighbor IPv4-UNDERLAY-PEERS peer group
   neighbor IPv4-UNDERLAY-PEERS send-community
   neighbor IPv4-UNDERLAY-PEERS maximum-routes 12000
   neighbor 10.2.2.129 peer group IPv4-UNDERLAY-PEERS
   neighbor 10.2.2.129 remote-as 65000
   neighbor 10.2.2.129 description r2-site2-wan1_Ethernet2
   neighbor 10.2.2.131 peer group IPv4-UNDERLAY-PEERS
   neighbor 10.2.2.131 remote-as 65000
   neighbor 10.2.2.131 description r2-site2-wan2_Ethernet2
   redistribute connected route-map RM-CONN-2-BGP
   !
   vlan 2
      rd 10.2.2.3:10002
      route-target both 10002:10002
      redistribute learned
   !
   vlan 254
      rd 10.2.2.3:10254
      route-target both 10254:10254
      redistribute learned
   !
   vlan 3
      rd 10.2.2.3:10003
      route-target both 10003:10003
      redistribute learned
   !
   vlan 4
      rd 10.2.2.3:10004
      route-target both 10004:10004
      redistribute learned
   !
   vlan 5
      rd 10.2.2.3:10005
      route-target both 10005:10005
      redistribute learned
   !
   vlan 6
      rd 10.2.2.3:10006
      route-target both 10006:10006
      redistribute learned
   !
   address-family evpn
      neighbor EVPN-OVERLAY-PEERS activate
   !
   address-family ipv4
      no neighbor EVPN-OVERLAY-PEERS activate
      neighbor IPv4-UNDERLAY-PEERS activate
   !
   vrf customer1
      rd 10.2.2.3:254
      route-target import evpn 254:254
      route-target export evpn 254:254
      router-id 10.2.2.3
      neighbor 10.2.2.129 remote-as 65000
      neighbor 10.2.2.129 peer group IPv4-UNDERLAY-PEERS
      neighbor 10.2.2.129 description r2-site2-wan1_Ethernet2.254_vrf_customer1
      neighbor 10.2.2.131 remote-as 65000
      neighbor 10.2.2.131 peer group IPv4-UNDERLAY-PEERS
      neighbor 10.2.2.131 description r2-site2-wan2_Ethernet2.254_vrf_customer1
      redistribute connected
   !
   vrf customer2
      rd 10.2.2.3:2
      route-target import evpn 2:2
      route-target export evpn 2:2
      router-id 10.2.2.3
      neighbor 10.2.2.129 remote-as 65000
      neighbor 10.2.2.129 peer group IPv4-UNDERLAY-PEERS
      neighbor 10.2.2.129 description r2-site2-wan1_Ethernet2.2_vrf_customer2
      neighbor 10.2.2.131 remote-as 65000
      neighbor 10.2.2.131 peer group IPv4-UNDERLAY-PEERS
      neighbor 10.2.2.131 description r2-site2-wan2_Ethernet2.2_vrf_customer2
      redistribute connected
   !
   vrf customer3
      rd 10.2.2.3:3
      route-target import evpn 3:3
      route-target export evpn 3:3
      router-id 10.2.2.3
      neighbor 10.2.2.129 remote-as 65000
      neighbor 10.2.2.129 peer group IPv4-UNDERLAY-PEERS
      neighbor 10.2.2.129 description r2-site2-wan1_Ethernet2.3_vrf_customer3
      neighbor 10.2.2.131 remote-as 65000
      neighbor 10.2.2.131 peer group IPv4-UNDERLAY-PEERS
      neighbor 10.2.2.131 description r2-site2-wan2_Ethernet2.3_vrf_customer3
      redistribute connected
   !
   vrf customer4
      rd 10.2.2.3:4
      route-target import evpn 4:4
      route-target export evpn 4:4
      router-id 10.2.2.3
      neighbor 10.2.2.129 remote-as 65000
      neighbor 10.2.2.129 peer group IPv4-UNDERLAY-PEERS
      neighbor 10.2.2.129 description r2-site2-wan1_Ethernet2.4_vrf_customer4
      neighbor 10.2.2.131 remote-as 65000
      neighbor 10.2.2.131 peer group IPv4-UNDERLAY-PEERS
      neighbor 10.2.2.131 description r2-site2-wan2_Ethernet2.4_vrf_customer4
      redistribute connected
   !
   vrf customer5
      rd 10.2.2.3:5
      route-target import evpn 5:5
      route-target export evpn 5:5
      router-id 10.2.2.3
      neighbor 10.2.2.129 remote-as 65000
      neighbor 10.2.2.129 peer group IPv4-UNDERLAY-PEERS
      neighbor 10.2.2.129 description r2-site2-wan1_Ethernet2.5_vrf_customer5
      neighbor 10.2.2.131 remote-as 65000
      neighbor 10.2.2.131 peer group IPv4-UNDERLAY-PEERS
      neighbor 10.2.2.131 description r2-site2-wan2_Ethernet2.5_vrf_customer5
      redistribute connected
   !
   vrf customer6
      rd 10.2.2.3:6
      route-target import evpn 6:6
      route-target export evpn 6:6
      router-id 10.2.2.3
      neighbor 10.2.2.129 remote-as 65000
      neighbor 10.2.2.129 peer group IPv4-UNDERLAY-PEERS
      neighbor 10.2.2.129 description r2-site2-wan1_Ethernet2.6_vrf_customer6
      neighbor 10.2.2.131 remote-as 65000
      neighbor 10.2.2.131 peer group IPv4-UNDERLAY-PEERS
      neighbor 10.2.2.131 description r2-site2-wan2_Ethernet2.6_vrf_customer6
      redistribute connected
```

## BFD

### Router BFD

#### Router BFD Multihop Summary

| Interval | Minimum RX | Multiplier |
| -------- | ---------- | ---------- |
| 1200 | 1200 | 3 |

#### Router BFD Device Configuration

```eos
!
router bfd
   multihop interval 1200 min-rx 1200 multiplier 3
```

## Multicast

### IP IGMP Snooping

#### IP IGMP Snooping Summary

| IGMP Snooping | Fast Leave | Interface Restart Query | Proxy | Restart Query Interval | Robustness Variable |
| ------------- | ---------- | ----------------------- | ----- | ---------------------- | ------------------- |
| Enabled | - | - | - | - | - |

#### IP IGMP Snooping Device Configuration

```eos
```

## Filters

### Prefix-lists

#### Prefix-lists Summary

##### PL-LOOPBACKS-EVPN-OVERLAY

| Sequence | Action |
| -------- | ------ |
| 10 | permit 10.2.2.0/26 eq 32 |
| 20 | permit 10.2.2.64/26 eq 32 |

#### Prefix-lists Device Configuration

```eos
!
ip prefix-list PL-LOOPBACKS-EVPN-OVERLAY
   seq 10 permit 10.2.2.0/26 eq 32
   seq 20 permit 10.2.2.64/26 eq 32
```

### Route-maps

#### Route-maps Summary

##### RM-CONN-2-BGP

| Sequence | Type | Match | Set | Sub-Route-Map | Continue |
| -------- | ---- | ----- | --- | ------------- | -------- |
| 10 | permit | ip address prefix-list PL-LOOPBACKS-EVPN-OVERLAY | - | - | - |

#### Route-maps Device Configuration

```eos
!
route-map RM-CONN-2-BGP permit 10
   match ip address prefix-list PL-LOOPBACKS-EVPN-OVERLAY
```

## VRF Instances

### VRF Instances Summary

| VRF Name | IP Routing |
| -------- | ---------- |
| customer1 | enabled |
| customer2 | enabled |
| customer3 | enabled |
| customer4 | enabled |
| customer5 | enabled |
| customer6 | enabled |
| MGMT | disabled |

### VRF Instances Device Configuration

```eos
!
vrf instance customer1
!
vrf instance customer2
!
vrf instance customer3
!
vrf instance customer4
!
vrf instance customer5
!
vrf instance customer6
!
vrf instance MGMT
```
