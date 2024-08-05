# r1-site1-leaf1

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
| Management1 | oob_management | oob | MGMT | 172.28.133.157/17 | 172.28.128.1 |

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
   ip address 172.28.133.157/17
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
| 7 | customer7 | - |
| 8 | customer8 | - |
| 9 | customer9 | - |
| 10 | customer10 | - |
| 11 | customer11 | - |
| 12 | customer12 | - |
| 13 | customer13 | - |
| 14 | customer14 | - |
| 15 | customer15 | - |
| 16 | customer16 | - |
| 17 | customer17 | - |
| 18 | customer18 | - |
| 19 | customer19 | - |
| 20 | customer20 | - |
| 21 | customer21 | - |
| 22 | customer22 | - |
| 23 | customer23 | - |
| 24 | customer24 | - |
| 25 | customer25 | - |
| 26 | customer26 | - |
| 27 | customer27 | - |
| 28 | customer28 | - |
| 29 | customer29 | - |
| 30 | customer30 | - |
| 31 | customer31 | - |
| 32 | customer32 | - |
| 33 | customer33 | - |
| 34 | customer34 | - |
| 35 | customer35 | - |
| 36 | customer36 | - |
| 37 | customer37 | - |
| 38 | customer38 | - |
| 39 | customer39 | - |
| 40 | customer40 | - |
| 41 | customer41 | - |
| 42 | customer42 | - |
| 43 | customer43 | - |
| 44 | customer44 | - |
| 45 | customer45 | - |
| 46 | customer46 | - |
| 47 | customer47 | - |
| 48 | customer48 | - |
| 49 | customer49 | - |
| 50 | customer50 | - |
| 51 | customer51 | - |
| 52 | customer52 | - |
| 53 | customer53 | - |
| 54 | customer54 | - |
| 55 | customer55 | - |
| 56 | customer56 | - |
| 57 | customer57 | - |
| 58 | customer58 | - |
| 59 | customer59 | - |
| 60 | customer60 | - |
| 61 | customer61 | - |
| 62 | customer62 | - |
| 63 | customer63 | - |
| 64 | customer64 | - |
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
vlan 7
   name customer7
!
vlan 8
   name customer8
!
vlan 9
   name customer9
!
vlan 10
   name customer10
!
vlan 11
   name customer11
!
vlan 12
   name customer12
!
vlan 13
   name customer13
!
vlan 14
   name customer14
!
vlan 15
   name customer15
!
vlan 16
   name customer16
!
vlan 17
   name customer17
!
vlan 18
   name customer18
!
vlan 19
   name customer19
!
vlan 20
   name customer20
!
vlan 21
   name customer21
!
vlan 22
   name customer22
!
vlan 23
   name customer23
!
vlan 24
   name customer24
!
vlan 25
   name customer25
!
vlan 26
   name customer26
!
vlan 27
   name customer27
!
vlan 28
   name customer28
!
vlan 29
   name customer29
!
vlan 30
   name customer30
!
vlan 31
   name customer31
!
vlan 32
   name customer32
!
vlan 33
   name customer33
!
vlan 34
   name customer34
!
vlan 35
   name customer35
!
vlan 36
   name customer36
!
vlan 37
   name customer37
!
vlan 38
   name customer38
!
vlan 39
   name customer39
!
vlan 40
   name customer40
!
vlan 41
   name customer41
!
vlan 42
   name customer42
!
vlan 43
   name customer43
!
vlan 44
   name customer44
!
vlan 45
   name customer45
!
vlan 46
   name customer46
!
vlan 47
   name customer47
!
vlan 48
   name customer48
!
vlan 49
   name customer49
!
vlan 50
   name customer50
!
vlan 51
   name customer51
!
vlan 52
   name customer52
!
vlan 53
   name customer53
!
vlan 54
   name customer54
!
vlan 55
   name customer55
!
vlan 56
   name customer56
!
vlan 57
   name customer57
!
vlan 58
   name customer58
!
vlan 59
   name customer59
!
vlan 60
   name customer60
!
vlan 61
   name customer61
!
vlan 62
   name customer62
!
vlan 63
   name customer63
!
vlan 64
   name customer64
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
| Ethernet49/1 |  test-host2 | trunk | 1-64,254 | - | - | - |

*Inherited from Port-Channel Interface

##### Encapsulation Dot1q Interfaces

| Interface | Description | Type | Vlan ID | Dot1q VLAN Tag |
| --------- | ----------- | -----| ------- | -------------- |
| Ethernet1.2 | P2P_LINK_TO_R1-SITE1-WAN1_Ethernet1/5.2_vrf_customer2 | l3dot1q | - | 2 |
| Ethernet1.3 | P2P_LINK_TO_R1-SITE1-WAN1_Ethernet1/5.3_vrf_customer3 | l3dot1q | - | 3 |
| Ethernet1.4 | P2P_LINK_TO_R1-SITE1-WAN1_Ethernet1/5.4_vrf_customer4 | l3dot1q | - | 4 |
| Ethernet1.5 | P2P_LINK_TO_R1-SITE1-WAN1_Ethernet1/5.5_vrf_customer5 | l3dot1q | - | 5 |
| Ethernet1.6 | P2P_LINK_TO_R1-SITE1-WAN1_Ethernet1/5.6_vrf_customer6 | l3dot1q | - | 6 |
| Ethernet1.7 | P2P_LINK_TO_R1-SITE1-WAN1_Ethernet1/5.7_vrf_customer7 | l3dot1q | - | 7 |
| Ethernet1.8 | P2P_LINK_TO_R1-SITE1-WAN1_Ethernet1/5.8_vrf_customer8 | l3dot1q | - | 8 |
| Ethernet1.9 | P2P_LINK_TO_R1-SITE1-WAN1_Ethernet1/5.9_vrf_customer9 | l3dot1q | - | 9 |
| Ethernet1.10 | P2P_LINK_TO_R1-SITE1-WAN1_Ethernet1/5.10_vrf_customer10 | l3dot1q | - | 10 |
| Ethernet1.11 | P2P_LINK_TO_R1-SITE1-WAN1_Ethernet1/5.11_vrf_customer11 | l3dot1q | - | 11 |
| Ethernet1.12 | P2P_LINK_TO_R1-SITE1-WAN1_Ethernet1/5.12_vrf_customer12 | l3dot1q | - | 12 |
| Ethernet1.13 | P2P_LINK_TO_R1-SITE1-WAN1_Ethernet1/5.13_vrf_customer13 | l3dot1q | - | 13 |
| Ethernet1.14 | P2P_LINK_TO_R1-SITE1-WAN1_Ethernet1/5.14_vrf_customer14 | l3dot1q | - | 14 |
| Ethernet1.15 | P2P_LINK_TO_R1-SITE1-WAN1_Ethernet1/5.15_vrf_customer15 | l3dot1q | - | 15 |
| Ethernet1.16 | P2P_LINK_TO_R1-SITE1-WAN1_Ethernet1/5.16_vrf_customer16 | l3dot1q | - | 16 |
| Ethernet1.17 | P2P_LINK_TO_R1-SITE1-WAN1_Ethernet1/5.17_vrf_customer17 | l3dot1q | - | 17 |
| Ethernet1.18 | P2P_LINK_TO_R1-SITE1-WAN1_Ethernet1/5.18_vrf_customer18 | l3dot1q | - | 18 |
| Ethernet1.19 | P2P_LINK_TO_R1-SITE1-WAN1_Ethernet1/5.19_vrf_customer19 | l3dot1q | - | 19 |
| Ethernet1.20 | P2P_LINK_TO_R1-SITE1-WAN1_Ethernet1/5.20_vrf_customer20 | l3dot1q | - | 20 |
| Ethernet1.21 | P2P_LINK_TO_R1-SITE1-WAN1_Ethernet1/5.21_vrf_customer21 | l3dot1q | - | 21 |
| Ethernet1.22 | P2P_LINK_TO_R1-SITE1-WAN1_Ethernet1/5.22_vrf_customer22 | l3dot1q | - | 22 |
| Ethernet1.23 | P2P_LINK_TO_R1-SITE1-WAN1_Ethernet1/5.23_vrf_customer23 | l3dot1q | - | 23 |
| Ethernet1.24 | P2P_LINK_TO_R1-SITE1-WAN1_Ethernet1/5.24_vrf_customer24 | l3dot1q | - | 24 |
| Ethernet1.25 | P2P_LINK_TO_R1-SITE1-WAN1_Ethernet1/5.25_vrf_customer25 | l3dot1q | - | 25 |
| Ethernet1.26 | P2P_LINK_TO_R1-SITE1-WAN1_Ethernet1/5.26_vrf_customer26 | l3dot1q | - | 26 |
| Ethernet1.27 | P2P_LINK_TO_R1-SITE1-WAN1_Ethernet1/5.27_vrf_customer27 | l3dot1q | - | 27 |
| Ethernet1.28 | P2P_LINK_TO_R1-SITE1-WAN1_Ethernet1/5.28_vrf_customer28 | l3dot1q | - | 28 |
| Ethernet1.29 | P2P_LINK_TO_R1-SITE1-WAN1_Ethernet1/5.29_vrf_customer29 | l3dot1q | - | 29 |
| Ethernet1.30 | P2P_LINK_TO_R1-SITE1-WAN1_Ethernet1/5.30_vrf_customer30 | l3dot1q | - | 30 |
| Ethernet1.31 | P2P_LINK_TO_R1-SITE1-WAN1_Ethernet1/5.31_vrf_customer31 | l3dot1q | - | 31 |
| Ethernet1.32 | P2P_LINK_TO_R1-SITE1-WAN1_Ethernet1/5.32_vrf_customer32 | l3dot1q | - | 32 |
| Ethernet1.33 | P2P_LINK_TO_R1-SITE1-WAN1_Ethernet1/5.33_vrf_customer33 | l3dot1q | - | 33 |
| Ethernet1.34 | P2P_LINK_TO_R1-SITE1-WAN1_Ethernet1/5.34_vrf_customer34 | l3dot1q | - | 34 |
| Ethernet1.35 | P2P_LINK_TO_R1-SITE1-WAN1_Ethernet1/5.35_vrf_customer35 | l3dot1q | - | 35 |
| Ethernet1.36 | P2P_LINK_TO_R1-SITE1-WAN1_Ethernet1/5.36_vrf_customer36 | l3dot1q | - | 36 |
| Ethernet1.37 | P2P_LINK_TO_R1-SITE1-WAN1_Ethernet1/5.37_vrf_customer37 | l3dot1q | - | 37 |
| Ethernet1.38 | P2P_LINK_TO_R1-SITE1-WAN1_Ethernet1/5.38_vrf_customer38 | l3dot1q | - | 38 |
| Ethernet1.39 | P2P_LINK_TO_R1-SITE1-WAN1_Ethernet1/5.39_vrf_customer39 | l3dot1q | - | 39 |
| Ethernet1.40 | P2P_LINK_TO_R1-SITE1-WAN1_Ethernet1/5.40_vrf_customer40 | l3dot1q | - | 40 |
| Ethernet1.41 | P2P_LINK_TO_R1-SITE1-WAN1_Ethernet1/5.41_vrf_customer41 | l3dot1q | - | 41 |
| Ethernet1.42 | P2P_LINK_TO_R1-SITE1-WAN1_Ethernet1/5.42_vrf_customer42 | l3dot1q | - | 42 |
| Ethernet1.43 | P2P_LINK_TO_R1-SITE1-WAN1_Ethernet1/5.43_vrf_customer43 | l3dot1q | - | 43 |
| Ethernet1.44 | P2P_LINK_TO_R1-SITE1-WAN1_Ethernet1/5.44_vrf_customer44 | l3dot1q | - | 44 |
| Ethernet1.45 | P2P_LINK_TO_R1-SITE1-WAN1_Ethernet1/5.45_vrf_customer45 | l3dot1q | - | 45 |
| Ethernet1.46 | P2P_LINK_TO_R1-SITE1-WAN1_Ethernet1/5.46_vrf_customer46 | l3dot1q | - | 46 |
| Ethernet1.47 | P2P_LINK_TO_R1-SITE1-WAN1_Ethernet1/5.47_vrf_customer47 | l3dot1q | - | 47 |
| Ethernet1.48 | P2P_LINK_TO_R1-SITE1-WAN1_Ethernet1/5.48_vrf_customer48 | l3dot1q | - | 48 |
| Ethernet1.49 | P2P_LINK_TO_R1-SITE1-WAN1_Ethernet1/5.49_vrf_customer49 | l3dot1q | - | 49 |
| Ethernet1.50 | P2P_LINK_TO_R1-SITE1-WAN1_Ethernet1/5.50_vrf_customer50 | l3dot1q | - | 50 |
| Ethernet1.51 | P2P_LINK_TO_R1-SITE1-WAN1_Ethernet1/5.51_vrf_customer51 | l3dot1q | - | 51 |
| Ethernet1.52 | P2P_LINK_TO_R1-SITE1-WAN1_Ethernet1/5.52_vrf_customer52 | l3dot1q | - | 52 |
| Ethernet1.53 | P2P_LINK_TO_R1-SITE1-WAN1_Ethernet1/5.53_vrf_customer53 | l3dot1q | - | 53 |
| Ethernet1.54 | P2P_LINK_TO_R1-SITE1-WAN1_Ethernet1/5.54_vrf_customer54 | l3dot1q | - | 54 |
| Ethernet1.55 | P2P_LINK_TO_R1-SITE1-WAN1_Ethernet1/5.55_vrf_customer55 | l3dot1q | - | 55 |
| Ethernet1.56 | P2P_LINK_TO_R1-SITE1-WAN1_Ethernet1/5.56_vrf_customer56 | l3dot1q | - | 56 |
| Ethernet1.57 | P2P_LINK_TO_R1-SITE1-WAN1_Ethernet1/5.57_vrf_customer57 | l3dot1q | - | 57 |
| Ethernet1.58 | P2P_LINK_TO_R1-SITE1-WAN1_Ethernet1/5.58_vrf_customer58 | l3dot1q | - | 58 |
| Ethernet1.59 | P2P_LINK_TO_R1-SITE1-WAN1_Ethernet1/5.59_vrf_customer59 | l3dot1q | - | 59 |
| Ethernet1.60 | P2P_LINK_TO_R1-SITE1-WAN1_Ethernet1/5.60_vrf_customer60 | l3dot1q | - | 60 |
| Ethernet1.61 | P2P_LINK_TO_R1-SITE1-WAN1_Ethernet1/5.61_vrf_customer61 | l3dot1q | - | 61 |
| Ethernet1.62 | P2P_LINK_TO_R1-SITE1-WAN1_Ethernet1/5.62_vrf_customer62 | l3dot1q | - | 62 |
| Ethernet1.63 | P2P_LINK_TO_R1-SITE1-WAN1_Ethernet1/5.63_vrf_customer63 | l3dot1q | - | 63 |
| Ethernet1.64 | P2P_LINK_TO_R1-SITE1-WAN1_Ethernet1/5.64_vrf_customer64 | l3dot1q | - | 64 |
| Ethernet1.254 | P2P_LINK_TO_R1-SITE1-WAN1_Ethernet1/5.254_vrf_customer1 | l3dot1q | - | 254 |
| Ethernet2.2 | P2P_LINK_TO_R1-SITE1-WAN2_Ethernet1/5.2_vrf_customer2 | l3dot1q | - | 2 |
| Ethernet2.3 | P2P_LINK_TO_R1-SITE1-WAN2_Ethernet1/5.3_vrf_customer3 | l3dot1q | - | 3 |
| Ethernet2.4 | P2P_LINK_TO_R1-SITE1-WAN2_Ethernet1/5.4_vrf_customer4 | l3dot1q | - | 4 |
| Ethernet2.5 | P2P_LINK_TO_R1-SITE1-WAN2_Ethernet1/5.5_vrf_customer5 | l3dot1q | - | 5 |
| Ethernet2.6 | P2P_LINK_TO_R1-SITE1-WAN2_Ethernet1/5.6_vrf_customer6 | l3dot1q | - | 6 |
| Ethernet2.7 | P2P_LINK_TO_R1-SITE1-WAN2_Ethernet1/5.7_vrf_customer7 | l3dot1q | - | 7 |
| Ethernet2.8 | P2P_LINK_TO_R1-SITE1-WAN2_Ethernet1/5.8_vrf_customer8 | l3dot1q | - | 8 |
| Ethernet2.9 | P2P_LINK_TO_R1-SITE1-WAN2_Ethernet1/5.9_vrf_customer9 | l3dot1q | - | 9 |
| Ethernet2.10 | P2P_LINK_TO_R1-SITE1-WAN2_Ethernet1/5.10_vrf_customer10 | l3dot1q | - | 10 |
| Ethernet2.11 | P2P_LINK_TO_R1-SITE1-WAN2_Ethernet1/5.11_vrf_customer11 | l3dot1q | - | 11 |
| Ethernet2.12 | P2P_LINK_TO_R1-SITE1-WAN2_Ethernet1/5.12_vrf_customer12 | l3dot1q | - | 12 |
| Ethernet2.13 | P2P_LINK_TO_R1-SITE1-WAN2_Ethernet1/5.13_vrf_customer13 | l3dot1q | - | 13 |
| Ethernet2.14 | P2P_LINK_TO_R1-SITE1-WAN2_Ethernet1/5.14_vrf_customer14 | l3dot1q | - | 14 |
| Ethernet2.15 | P2P_LINK_TO_R1-SITE1-WAN2_Ethernet1/5.15_vrf_customer15 | l3dot1q | - | 15 |
| Ethernet2.16 | P2P_LINK_TO_R1-SITE1-WAN2_Ethernet1/5.16_vrf_customer16 | l3dot1q | - | 16 |
| Ethernet2.17 | P2P_LINK_TO_R1-SITE1-WAN2_Ethernet1/5.17_vrf_customer17 | l3dot1q | - | 17 |
| Ethernet2.18 | P2P_LINK_TO_R1-SITE1-WAN2_Ethernet1/5.18_vrf_customer18 | l3dot1q | - | 18 |
| Ethernet2.19 | P2P_LINK_TO_R1-SITE1-WAN2_Ethernet1/5.19_vrf_customer19 | l3dot1q | - | 19 |
| Ethernet2.20 | P2P_LINK_TO_R1-SITE1-WAN2_Ethernet1/5.20_vrf_customer20 | l3dot1q | - | 20 |
| Ethernet2.21 | P2P_LINK_TO_R1-SITE1-WAN2_Ethernet1/5.21_vrf_customer21 | l3dot1q | - | 21 |
| Ethernet2.22 | P2P_LINK_TO_R1-SITE1-WAN2_Ethernet1/5.22_vrf_customer22 | l3dot1q | - | 22 |
| Ethernet2.23 | P2P_LINK_TO_R1-SITE1-WAN2_Ethernet1/5.23_vrf_customer23 | l3dot1q | - | 23 |
| Ethernet2.24 | P2P_LINK_TO_R1-SITE1-WAN2_Ethernet1/5.24_vrf_customer24 | l3dot1q | - | 24 |
| Ethernet2.25 | P2P_LINK_TO_R1-SITE1-WAN2_Ethernet1/5.25_vrf_customer25 | l3dot1q | - | 25 |
| Ethernet2.26 | P2P_LINK_TO_R1-SITE1-WAN2_Ethernet1/5.26_vrf_customer26 | l3dot1q | - | 26 |
| Ethernet2.27 | P2P_LINK_TO_R1-SITE1-WAN2_Ethernet1/5.27_vrf_customer27 | l3dot1q | - | 27 |
| Ethernet2.28 | P2P_LINK_TO_R1-SITE1-WAN2_Ethernet1/5.28_vrf_customer28 | l3dot1q | - | 28 |
| Ethernet2.29 | P2P_LINK_TO_R1-SITE1-WAN2_Ethernet1/5.29_vrf_customer29 | l3dot1q | - | 29 |
| Ethernet2.30 | P2P_LINK_TO_R1-SITE1-WAN2_Ethernet1/5.30_vrf_customer30 | l3dot1q | - | 30 |
| Ethernet2.31 | P2P_LINK_TO_R1-SITE1-WAN2_Ethernet1/5.31_vrf_customer31 | l3dot1q | - | 31 |
| Ethernet2.32 | P2P_LINK_TO_R1-SITE1-WAN2_Ethernet1/5.32_vrf_customer32 | l3dot1q | - | 32 |
| Ethernet2.33 | P2P_LINK_TO_R1-SITE1-WAN2_Ethernet1/5.33_vrf_customer33 | l3dot1q | - | 33 |
| Ethernet2.34 | P2P_LINK_TO_R1-SITE1-WAN2_Ethernet1/5.34_vrf_customer34 | l3dot1q | - | 34 |
| Ethernet2.35 | P2P_LINK_TO_R1-SITE1-WAN2_Ethernet1/5.35_vrf_customer35 | l3dot1q | - | 35 |
| Ethernet2.36 | P2P_LINK_TO_R1-SITE1-WAN2_Ethernet1/5.36_vrf_customer36 | l3dot1q | - | 36 |
| Ethernet2.37 | P2P_LINK_TO_R1-SITE1-WAN2_Ethernet1/5.37_vrf_customer37 | l3dot1q | - | 37 |
| Ethernet2.38 | P2P_LINK_TO_R1-SITE1-WAN2_Ethernet1/5.38_vrf_customer38 | l3dot1q | - | 38 |
| Ethernet2.39 | P2P_LINK_TO_R1-SITE1-WAN2_Ethernet1/5.39_vrf_customer39 | l3dot1q | - | 39 |
| Ethernet2.40 | P2P_LINK_TO_R1-SITE1-WAN2_Ethernet1/5.40_vrf_customer40 | l3dot1q | - | 40 |
| Ethernet2.41 | P2P_LINK_TO_R1-SITE1-WAN2_Ethernet1/5.41_vrf_customer41 | l3dot1q | - | 41 |
| Ethernet2.42 | P2P_LINK_TO_R1-SITE1-WAN2_Ethernet1/5.42_vrf_customer42 | l3dot1q | - | 42 |
| Ethernet2.43 | P2P_LINK_TO_R1-SITE1-WAN2_Ethernet1/5.43_vrf_customer43 | l3dot1q | - | 43 |
| Ethernet2.44 | P2P_LINK_TO_R1-SITE1-WAN2_Ethernet1/5.44_vrf_customer44 | l3dot1q | - | 44 |
| Ethernet2.45 | P2P_LINK_TO_R1-SITE1-WAN2_Ethernet1/5.45_vrf_customer45 | l3dot1q | - | 45 |
| Ethernet2.46 | P2P_LINK_TO_R1-SITE1-WAN2_Ethernet1/5.46_vrf_customer46 | l3dot1q | - | 46 |
| Ethernet2.47 | P2P_LINK_TO_R1-SITE1-WAN2_Ethernet1/5.47_vrf_customer47 | l3dot1q | - | 47 |
| Ethernet2.48 | P2P_LINK_TO_R1-SITE1-WAN2_Ethernet1/5.48_vrf_customer48 | l3dot1q | - | 48 |
| Ethernet2.49 | P2P_LINK_TO_R1-SITE1-WAN2_Ethernet1/5.49_vrf_customer49 | l3dot1q | - | 49 |
| Ethernet2.50 | P2P_LINK_TO_R1-SITE1-WAN2_Ethernet1/5.50_vrf_customer50 | l3dot1q | - | 50 |
| Ethernet2.51 | P2P_LINK_TO_R1-SITE1-WAN2_Ethernet1/5.51_vrf_customer51 | l3dot1q | - | 51 |
| Ethernet2.52 | P2P_LINK_TO_R1-SITE1-WAN2_Ethernet1/5.52_vrf_customer52 | l3dot1q | - | 52 |
| Ethernet2.53 | P2P_LINK_TO_R1-SITE1-WAN2_Ethernet1/5.53_vrf_customer53 | l3dot1q | - | 53 |
| Ethernet2.54 | P2P_LINK_TO_R1-SITE1-WAN2_Ethernet1/5.54_vrf_customer54 | l3dot1q | - | 54 |
| Ethernet2.55 | P2P_LINK_TO_R1-SITE1-WAN2_Ethernet1/5.55_vrf_customer55 | l3dot1q | - | 55 |
| Ethernet2.56 | P2P_LINK_TO_R1-SITE1-WAN2_Ethernet1/5.56_vrf_customer56 | l3dot1q | - | 56 |
| Ethernet2.57 | P2P_LINK_TO_R1-SITE1-WAN2_Ethernet1/5.57_vrf_customer57 | l3dot1q | - | 57 |
| Ethernet2.58 | P2P_LINK_TO_R1-SITE1-WAN2_Ethernet1/5.58_vrf_customer58 | l3dot1q | - | 58 |
| Ethernet2.59 | P2P_LINK_TO_R1-SITE1-WAN2_Ethernet1/5.59_vrf_customer59 | l3dot1q | - | 59 |
| Ethernet2.60 | P2P_LINK_TO_R1-SITE1-WAN2_Ethernet1/5.60_vrf_customer60 | l3dot1q | - | 60 |
| Ethernet2.61 | P2P_LINK_TO_R1-SITE1-WAN2_Ethernet1/5.61_vrf_customer61 | l3dot1q | - | 61 |
| Ethernet2.62 | P2P_LINK_TO_R1-SITE1-WAN2_Ethernet1/5.62_vrf_customer62 | l3dot1q | - | 62 |
| Ethernet2.63 | P2P_LINK_TO_R1-SITE1-WAN2_Ethernet1/5.63_vrf_customer63 | l3dot1q | - | 63 |
| Ethernet2.64 | P2P_LINK_TO_R1-SITE1-WAN2_Ethernet1/5.64_vrf_customer64 | l3dot1q | - | 64 |
| Ethernet2.254 | P2P_LINK_TO_R1-SITE1-WAN2_Ethernet1/5.254_vrf_customer1 | l3dot1q | - | 254 |

##### IPv4

| Interface | Description | Type | Channel Group | IP Address | VRF |  MTU | Shutdown | ACL In | ACL Out |
| --------- | ----------- | -----| ------------- | ---------- | ----| ---- | -------- | ------ | ------- |
| Ethernet1 | P2P_LINK_TO_R1-SITE1-WAN1_Ethernet1/5 | routed | - | 10.1.1.128/31 | default | 9214 | False | - | - |
| Ethernet1.2 | P2P_LINK_TO_R1-SITE1-WAN1_Ethernet1/5.2_vrf_customer2 | l3dot1q | - | 10.1.1.128/31 | customer2 | 9214 | False | - | - |
| Ethernet1.3 | P2P_LINK_TO_R1-SITE1-WAN1_Ethernet1/5.3_vrf_customer3 | l3dot1q | - | 10.1.1.128/31 | customer3 | 9214 | False | - | - |
| Ethernet1.4 | P2P_LINK_TO_R1-SITE1-WAN1_Ethernet1/5.4_vrf_customer4 | l3dot1q | - | 10.1.1.128/31 | customer4 | 9214 | False | - | - |
| Ethernet1.5 | P2P_LINK_TO_R1-SITE1-WAN1_Ethernet1/5.5_vrf_customer5 | l3dot1q | - | 10.1.1.128/31 | customer5 | 9214 | False | - | - |
| Ethernet1.6 | P2P_LINK_TO_R1-SITE1-WAN1_Ethernet1/5.6_vrf_customer6 | l3dot1q | - | 10.1.1.128/31 | customer6 | 9214 | False | - | - |
| Ethernet1.7 | P2P_LINK_TO_R1-SITE1-WAN1_Ethernet1/5.7_vrf_customer7 | l3dot1q | - | 10.1.1.128/31 | customer7 | 9214 | False | - | - |
| Ethernet1.8 | P2P_LINK_TO_R1-SITE1-WAN1_Ethernet1/5.8_vrf_customer8 | l3dot1q | - | 10.1.1.128/31 | customer8 | 9214 | False | - | - |
| Ethernet1.9 | P2P_LINK_TO_R1-SITE1-WAN1_Ethernet1/5.9_vrf_customer9 | l3dot1q | - | 10.1.1.128/31 | customer9 | 9214 | False | - | - |
| Ethernet1.10 | P2P_LINK_TO_R1-SITE1-WAN1_Ethernet1/5.10_vrf_customer10 | l3dot1q | - | 10.1.1.128/31 | customer10 | 9214 | False | - | - |
| Ethernet1.11 | P2P_LINK_TO_R1-SITE1-WAN1_Ethernet1/5.11_vrf_customer11 | l3dot1q | - | 10.1.1.128/31 | customer11 | 9214 | False | - | - |
| Ethernet1.12 | P2P_LINK_TO_R1-SITE1-WAN1_Ethernet1/5.12_vrf_customer12 | l3dot1q | - | 10.1.1.128/31 | customer12 | 9214 | False | - | - |
| Ethernet1.13 | P2P_LINK_TO_R1-SITE1-WAN1_Ethernet1/5.13_vrf_customer13 | l3dot1q | - | 10.1.1.128/31 | customer13 | 9214 | False | - | - |
| Ethernet1.14 | P2P_LINK_TO_R1-SITE1-WAN1_Ethernet1/5.14_vrf_customer14 | l3dot1q | - | 10.1.1.128/31 | customer14 | 9214 | False | - | - |
| Ethernet1.15 | P2P_LINK_TO_R1-SITE1-WAN1_Ethernet1/5.15_vrf_customer15 | l3dot1q | - | 10.1.1.128/31 | customer15 | 9214 | False | - | - |
| Ethernet1.16 | P2P_LINK_TO_R1-SITE1-WAN1_Ethernet1/5.16_vrf_customer16 | l3dot1q | - | 10.1.1.128/31 | customer16 | 9214 | False | - | - |
| Ethernet1.17 | P2P_LINK_TO_R1-SITE1-WAN1_Ethernet1/5.17_vrf_customer17 | l3dot1q | - | 10.1.1.128/31 | customer17 | 9214 | False | - | - |
| Ethernet1.18 | P2P_LINK_TO_R1-SITE1-WAN1_Ethernet1/5.18_vrf_customer18 | l3dot1q | - | 10.1.1.128/31 | customer18 | 9214 | False | - | - |
| Ethernet1.19 | P2P_LINK_TO_R1-SITE1-WAN1_Ethernet1/5.19_vrf_customer19 | l3dot1q | - | 10.1.1.128/31 | customer19 | 9214 | False | - | - |
| Ethernet1.20 | P2P_LINK_TO_R1-SITE1-WAN1_Ethernet1/5.20_vrf_customer20 | l3dot1q | - | 10.1.1.128/31 | customer20 | 9214 | False | - | - |
| Ethernet1.21 | P2P_LINK_TO_R1-SITE1-WAN1_Ethernet1/5.21_vrf_customer21 | l3dot1q | - | 10.1.1.128/31 | customer21 | 9214 | False | - | - |
| Ethernet1.22 | P2P_LINK_TO_R1-SITE1-WAN1_Ethernet1/5.22_vrf_customer22 | l3dot1q | - | 10.1.1.128/31 | customer22 | 9214 | False | - | - |
| Ethernet1.23 | P2P_LINK_TO_R1-SITE1-WAN1_Ethernet1/5.23_vrf_customer23 | l3dot1q | - | 10.1.1.128/31 | customer23 | 9214 | False | - | - |
| Ethernet1.24 | P2P_LINK_TO_R1-SITE1-WAN1_Ethernet1/5.24_vrf_customer24 | l3dot1q | - | 10.1.1.128/31 | customer24 | 9214 | False | - | - |
| Ethernet1.25 | P2P_LINK_TO_R1-SITE1-WAN1_Ethernet1/5.25_vrf_customer25 | l3dot1q | - | 10.1.1.128/31 | customer25 | 9214 | False | - | - |
| Ethernet1.26 | P2P_LINK_TO_R1-SITE1-WAN1_Ethernet1/5.26_vrf_customer26 | l3dot1q | - | 10.1.1.128/31 | customer26 | 9214 | False | - | - |
| Ethernet1.27 | P2P_LINK_TO_R1-SITE1-WAN1_Ethernet1/5.27_vrf_customer27 | l3dot1q | - | 10.1.1.128/31 | customer27 | 9214 | False | - | - |
| Ethernet1.28 | P2P_LINK_TO_R1-SITE1-WAN1_Ethernet1/5.28_vrf_customer28 | l3dot1q | - | 10.1.1.128/31 | customer28 | 9214 | False | - | - |
| Ethernet1.29 | P2P_LINK_TO_R1-SITE1-WAN1_Ethernet1/5.29_vrf_customer29 | l3dot1q | - | 10.1.1.128/31 | customer29 | 9214 | False | - | - |
| Ethernet1.30 | P2P_LINK_TO_R1-SITE1-WAN1_Ethernet1/5.30_vrf_customer30 | l3dot1q | - | 10.1.1.128/31 | customer30 | 9214 | False | - | - |
| Ethernet1.31 | P2P_LINK_TO_R1-SITE1-WAN1_Ethernet1/5.31_vrf_customer31 | l3dot1q | - | 10.1.1.128/31 | customer31 | 9214 | False | - | - |
| Ethernet1.32 | P2P_LINK_TO_R1-SITE1-WAN1_Ethernet1/5.32_vrf_customer32 | l3dot1q | - | 10.1.1.128/31 | customer32 | 9214 | False | - | - |
| Ethernet1.33 | P2P_LINK_TO_R1-SITE1-WAN1_Ethernet1/5.33_vrf_customer33 | l3dot1q | - | 10.1.1.128/31 | customer33 | 9214 | False | - | - |
| Ethernet1.34 | P2P_LINK_TO_R1-SITE1-WAN1_Ethernet1/5.34_vrf_customer34 | l3dot1q | - | 10.1.1.128/31 | customer34 | 9214 | False | - | - |
| Ethernet1.35 | P2P_LINK_TO_R1-SITE1-WAN1_Ethernet1/5.35_vrf_customer35 | l3dot1q | - | 10.1.1.128/31 | customer35 | 9214 | False | - | - |
| Ethernet1.36 | P2P_LINK_TO_R1-SITE1-WAN1_Ethernet1/5.36_vrf_customer36 | l3dot1q | - | 10.1.1.128/31 | customer36 | 9214 | False | - | - |
| Ethernet1.37 | P2P_LINK_TO_R1-SITE1-WAN1_Ethernet1/5.37_vrf_customer37 | l3dot1q | - | 10.1.1.128/31 | customer37 | 9214 | False | - | - |
| Ethernet1.38 | P2P_LINK_TO_R1-SITE1-WAN1_Ethernet1/5.38_vrf_customer38 | l3dot1q | - | 10.1.1.128/31 | customer38 | 9214 | False | - | - |
| Ethernet1.39 | P2P_LINK_TO_R1-SITE1-WAN1_Ethernet1/5.39_vrf_customer39 | l3dot1q | - | 10.1.1.128/31 | customer39 | 9214 | False | - | - |
| Ethernet1.40 | P2P_LINK_TO_R1-SITE1-WAN1_Ethernet1/5.40_vrf_customer40 | l3dot1q | - | 10.1.1.128/31 | customer40 | 9214 | False | - | - |
| Ethernet1.41 | P2P_LINK_TO_R1-SITE1-WAN1_Ethernet1/5.41_vrf_customer41 | l3dot1q | - | 10.1.1.128/31 | customer41 | 9214 | False | - | - |
| Ethernet1.42 | P2P_LINK_TO_R1-SITE1-WAN1_Ethernet1/5.42_vrf_customer42 | l3dot1q | - | 10.1.1.128/31 | customer42 | 9214 | False | - | - |
| Ethernet1.43 | P2P_LINK_TO_R1-SITE1-WAN1_Ethernet1/5.43_vrf_customer43 | l3dot1q | - | 10.1.1.128/31 | customer43 | 9214 | False | - | - |
| Ethernet1.44 | P2P_LINK_TO_R1-SITE1-WAN1_Ethernet1/5.44_vrf_customer44 | l3dot1q | - | 10.1.1.128/31 | customer44 | 9214 | False | - | - |
| Ethernet1.45 | P2P_LINK_TO_R1-SITE1-WAN1_Ethernet1/5.45_vrf_customer45 | l3dot1q | - | 10.1.1.128/31 | customer45 | 9214 | False | - | - |
| Ethernet1.46 | P2P_LINK_TO_R1-SITE1-WAN1_Ethernet1/5.46_vrf_customer46 | l3dot1q | - | 10.1.1.128/31 | customer46 | 9214 | False | - | - |
| Ethernet1.47 | P2P_LINK_TO_R1-SITE1-WAN1_Ethernet1/5.47_vrf_customer47 | l3dot1q | - | 10.1.1.128/31 | customer47 | 9214 | False | - | - |
| Ethernet1.48 | P2P_LINK_TO_R1-SITE1-WAN1_Ethernet1/5.48_vrf_customer48 | l3dot1q | - | 10.1.1.128/31 | customer48 | 9214 | False | - | - |
| Ethernet1.49 | P2P_LINK_TO_R1-SITE1-WAN1_Ethernet1/5.49_vrf_customer49 | l3dot1q | - | 10.1.1.128/31 | customer49 | 9214 | False | - | - |
| Ethernet1.50 | P2P_LINK_TO_R1-SITE1-WAN1_Ethernet1/5.50_vrf_customer50 | l3dot1q | - | 10.1.1.128/31 | customer50 | 9214 | False | - | - |
| Ethernet1.51 | P2P_LINK_TO_R1-SITE1-WAN1_Ethernet1/5.51_vrf_customer51 | l3dot1q | - | 10.1.1.128/31 | customer51 | 9214 | False | - | - |
| Ethernet1.52 | P2P_LINK_TO_R1-SITE1-WAN1_Ethernet1/5.52_vrf_customer52 | l3dot1q | - | 10.1.1.128/31 | customer52 | 9214 | False | - | - |
| Ethernet1.53 | P2P_LINK_TO_R1-SITE1-WAN1_Ethernet1/5.53_vrf_customer53 | l3dot1q | - | 10.1.1.128/31 | customer53 | 9214 | False | - | - |
| Ethernet1.54 | P2P_LINK_TO_R1-SITE1-WAN1_Ethernet1/5.54_vrf_customer54 | l3dot1q | - | 10.1.1.128/31 | customer54 | 9214 | False | - | - |
| Ethernet1.55 | P2P_LINK_TO_R1-SITE1-WAN1_Ethernet1/5.55_vrf_customer55 | l3dot1q | - | 10.1.1.128/31 | customer55 | 9214 | False | - | - |
| Ethernet1.56 | P2P_LINK_TO_R1-SITE1-WAN1_Ethernet1/5.56_vrf_customer56 | l3dot1q | - | 10.1.1.128/31 | customer56 | 9214 | False | - | - |
| Ethernet1.57 | P2P_LINK_TO_R1-SITE1-WAN1_Ethernet1/5.57_vrf_customer57 | l3dot1q | - | 10.1.1.128/31 | customer57 | 9214 | False | - | - |
| Ethernet1.58 | P2P_LINK_TO_R1-SITE1-WAN1_Ethernet1/5.58_vrf_customer58 | l3dot1q | - | 10.1.1.128/31 | customer58 | 9214 | False | - | - |
| Ethernet1.59 | P2P_LINK_TO_R1-SITE1-WAN1_Ethernet1/5.59_vrf_customer59 | l3dot1q | - | 10.1.1.128/31 | customer59 | 9214 | False | - | - |
| Ethernet1.60 | P2P_LINK_TO_R1-SITE1-WAN1_Ethernet1/5.60_vrf_customer60 | l3dot1q | - | 10.1.1.128/31 | customer60 | 9214 | False | - | - |
| Ethernet1.61 | P2P_LINK_TO_R1-SITE1-WAN1_Ethernet1/5.61_vrf_customer61 | l3dot1q | - | 10.1.1.128/31 | customer61 | 9214 | False | - | - |
| Ethernet1.62 | P2P_LINK_TO_R1-SITE1-WAN1_Ethernet1/5.62_vrf_customer62 | l3dot1q | - | 10.1.1.128/31 | customer62 | 9214 | False | - | - |
| Ethernet1.63 | P2P_LINK_TO_R1-SITE1-WAN1_Ethernet1/5.63_vrf_customer63 | l3dot1q | - | 10.1.1.128/31 | customer63 | 9214 | False | - | - |
| Ethernet1.64 | P2P_LINK_TO_R1-SITE1-WAN1_Ethernet1/5.64_vrf_customer64 | l3dot1q | - | 10.1.1.128/31 | customer64 | 9214 | False | - | - |
| Ethernet1.254 | P2P_LINK_TO_R1-SITE1-WAN1_Ethernet1/5.254_vrf_customer1 | l3dot1q | - | 10.1.1.128/31 | customer1 | 9214 | False | - | - |
| Ethernet2 | P2P_LINK_TO_R1-SITE1-WAN2_Ethernet1/5 | routed | - | 10.1.1.130/31 | default | 9214 | False | - | - |
| Ethernet2.2 | P2P_LINK_TO_R1-SITE1-WAN2_Ethernet1/5.2_vrf_customer2 | l3dot1q | - | 10.1.1.130/31 | customer2 | 9214 | False | - | - |
| Ethernet2.3 | P2P_LINK_TO_R1-SITE1-WAN2_Ethernet1/5.3_vrf_customer3 | l3dot1q | - | 10.1.1.130/31 | customer3 | 9214 | False | - | - |
| Ethernet2.4 | P2P_LINK_TO_R1-SITE1-WAN2_Ethernet1/5.4_vrf_customer4 | l3dot1q | - | 10.1.1.130/31 | customer4 | 9214 | False | - | - |
| Ethernet2.5 | P2P_LINK_TO_R1-SITE1-WAN2_Ethernet1/5.5_vrf_customer5 | l3dot1q | - | 10.1.1.130/31 | customer5 | 9214 | False | - | - |
| Ethernet2.6 | P2P_LINK_TO_R1-SITE1-WAN2_Ethernet1/5.6_vrf_customer6 | l3dot1q | - | 10.1.1.130/31 | customer6 | 9214 | False | - | - |
| Ethernet2.7 | P2P_LINK_TO_R1-SITE1-WAN2_Ethernet1/5.7_vrf_customer7 | l3dot1q | - | 10.1.1.130/31 | customer7 | 9214 | False | - | - |
| Ethernet2.8 | P2P_LINK_TO_R1-SITE1-WAN2_Ethernet1/5.8_vrf_customer8 | l3dot1q | - | 10.1.1.130/31 | customer8 | 9214 | False | - | - |
| Ethernet2.9 | P2P_LINK_TO_R1-SITE1-WAN2_Ethernet1/5.9_vrf_customer9 | l3dot1q | - | 10.1.1.130/31 | customer9 | 9214 | False | - | - |
| Ethernet2.10 | P2P_LINK_TO_R1-SITE1-WAN2_Ethernet1/5.10_vrf_customer10 | l3dot1q | - | 10.1.1.130/31 | customer10 | 9214 | False | - | - |
| Ethernet2.11 | P2P_LINK_TO_R1-SITE1-WAN2_Ethernet1/5.11_vrf_customer11 | l3dot1q | - | 10.1.1.130/31 | customer11 | 9214 | False | - | - |
| Ethernet2.12 | P2P_LINK_TO_R1-SITE1-WAN2_Ethernet1/5.12_vrf_customer12 | l3dot1q | - | 10.1.1.130/31 | customer12 | 9214 | False | - | - |
| Ethernet2.13 | P2P_LINK_TO_R1-SITE1-WAN2_Ethernet1/5.13_vrf_customer13 | l3dot1q | - | 10.1.1.130/31 | customer13 | 9214 | False | - | - |
| Ethernet2.14 | P2P_LINK_TO_R1-SITE1-WAN2_Ethernet1/5.14_vrf_customer14 | l3dot1q | - | 10.1.1.130/31 | customer14 | 9214 | False | - | - |
| Ethernet2.15 | P2P_LINK_TO_R1-SITE1-WAN2_Ethernet1/5.15_vrf_customer15 | l3dot1q | - | 10.1.1.130/31 | customer15 | 9214 | False | - | - |
| Ethernet2.16 | P2P_LINK_TO_R1-SITE1-WAN2_Ethernet1/5.16_vrf_customer16 | l3dot1q | - | 10.1.1.130/31 | customer16 | 9214 | False | - | - |
| Ethernet2.17 | P2P_LINK_TO_R1-SITE1-WAN2_Ethernet1/5.17_vrf_customer17 | l3dot1q | - | 10.1.1.130/31 | customer17 | 9214 | False | - | - |
| Ethernet2.18 | P2P_LINK_TO_R1-SITE1-WAN2_Ethernet1/5.18_vrf_customer18 | l3dot1q | - | 10.1.1.130/31 | customer18 | 9214 | False | - | - |
| Ethernet2.19 | P2P_LINK_TO_R1-SITE1-WAN2_Ethernet1/5.19_vrf_customer19 | l3dot1q | - | 10.1.1.130/31 | customer19 | 9214 | False | - | - |
| Ethernet2.20 | P2P_LINK_TO_R1-SITE1-WAN2_Ethernet1/5.20_vrf_customer20 | l3dot1q | - | 10.1.1.130/31 | customer20 | 9214 | False | - | - |
| Ethernet2.21 | P2P_LINK_TO_R1-SITE1-WAN2_Ethernet1/5.21_vrf_customer21 | l3dot1q | - | 10.1.1.130/31 | customer21 | 9214 | False | - | - |
| Ethernet2.22 | P2P_LINK_TO_R1-SITE1-WAN2_Ethernet1/5.22_vrf_customer22 | l3dot1q | - | 10.1.1.130/31 | customer22 | 9214 | False | - | - |
| Ethernet2.23 | P2P_LINK_TO_R1-SITE1-WAN2_Ethernet1/5.23_vrf_customer23 | l3dot1q | - | 10.1.1.130/31 | customer23 | 9214 | False | - | - |
| Ethernet2.24 | P2P_LINK_TO_R1-SITE1-WAN2_Ethernet1/5.24_vrf_customer24 | l3dot1q | - | 10.1.1.130/31 | customer24 | 9214 | False | - | - |
| Ethernet2.25 | P2P_LINK_TO_R1-SITE1-WAN2_Ethernet1/5.25_vrf_customer25 | l3dot1q | - | 10.1.1.130/31 | customer25 | 9214 | False | - | - |
| Ethernet2.26 | P2P_LINK_TO_R1-SITE1-WAN2_Ethernet1/5.26_vrf_customer26 | l3dot1q | - | 10.1.1.130/31 | customer26 | 9214 | False | - | - |
| Ethernet2.27 | P2P_LINK_TO_R1-SITE1-WAN2_Ethernet1/5.27_vrf_customer27 | l3dot1q | - | 10.1.1.130/31 | customer27 | 9214 | False | - | - |
| Ethernet2.28 | P2P_LINK_TO_R1-SITE1-WAN2_Ethernet1/5.28_vrf_customer28 | l3dot1q | - | 10.1.1.130/31 | customer28 | 9214 | False | - | - |
| Ethernet2.29 | P2P_LINK_TO_R1-SITE1-WAN2_Ethernet1/5.29_vrf_customer29 | l3dot1q | - | 10.1.1.130/31 | customer29 | 9214 | False | - | - |
| Ethernet2.30 | P2P_LINK_TO_R1-SITE1-WAN2_Ethernet1/5.30_vrf_customer30 | l3dot1q | - | 10.1.1.130/31 | customer30 | 9214 | False | - | - |
| Ethernet2.31 | P2P_LINK_TO_R1-SITE1-WAN2_Ethernet1/5.31_vrf_customer31 | l3dot1q | - | 10.1.1.130/31 | customer31 | 9214 | False | - | - |
| Ethernet2.32 | P2P_LINK_TO_R1-SITE1-WAN2_Ethernet1/5.32_vrf_customer32 | l3dot1q | - | 10.1.1.130/31 | customer32 | 9214 | False | - | - |
| Ethernet2.33 | P2P_LINK_TO_R1-SITE1-WAN2_Ethernet1/5.33_vrf_customer33 | l3dot1q | - | 10.1.1.130/31 | customer33 | 9214 | False | - | - |
| Ethernet2.34 | P2P_LINK_TO_R1-SITE1-WAN2_Ethernet1/5.34_vrf_customer34 | l3dot1q | - | 10.1.1.130/31 | customer34 | 9214 | False | - | - |
| Ethernet2.35 | P2P_LINK_TO_R1-SITE1-WAN2_Ethernet1/5.35_vrf_customer35 | l3dot1q | - | 10.1.1.130/31 | customer35 | 9214 | False | - | - |
| Ethernet2.36 | P2P_LINK_TO_R1-SITE1-WAN2_Ethernet1/5.36_vrf_customer36 | l3dot1q | - | 10.1.1.130/31 | customer36 | 9214 | False | - | - |
| Ethernet2.37 | P2P_LINK_TO_R1-SITE1-WAN2_Ethernet1/5.37_vrf_customer37 | l3dot1q | - | 10.1.1.130/31 | customer37 | 9214 | False | - | - |
| Ethernet2.38 | P2P_LINK_TO_R1-SITE1-WAN2_Ethernet1/5.38_vrf_customer38 | l3dot1q | - | 10.1.1.130/31 | customer38 | 9214 | False | - | - |
| Ethernet2.39 | P2P_LINK_TO_R1-SITE1-WAN2_Ethernet1/5.39_vrf_customer39 | l3dot1q | - | 10.1.1.130/31 | customer39 | 9214 | False | - | - |
| Ethernet2.40 | P2P_LINK_TO_R1-SITE1-WAN2_Ethernet1/5.40_vrf_customer40 | l3dot1q | - | 10.1.1.130/31 | customer40 | 9214 | False | - | - |
| Ethernet2.41 | P2P_LINK_TO_R1-SITE1-WAN2_Ethernet1/5.41_vrf_customer41 | l3dot1q | - | 10.1.1.130/31 | customer41 | 9214 | False | - | - |
| Ethernet2.42 | P2P_LINK_TO_R1-SITE1-WAN2_Ethernet1/5.42_vrf_customer42 | l3dot1q | - | 10.1.1.130/31 | customer42 | 9214 | False | - | - |
| Ethernet2.43 | P2P_LINK_TO_R1-SITE1-WAN2_Ethernet1/5.43_vrf_customer43 | l3dot1q | - | 10.1.1.130/31 | customer43 | 9214 | False | - | - |
| Ethernet2.44 | P2P_LINK_TO_R1-SITE1-WAN2_Ethernet1/5.44_vrf_customer44 | l3dot1q | - | 10.1.1.130/31 | customer44 | 9214 | False | - | - |
| Ethernet2.45 | P2P_LINK_TO_R1-SITE1-WAN2_Ethernet1/5.45_vrf_customer45 | l3dot1q | - | 10.1.1.130/31 | customer45 | 9214 | False | - | - |
| Ethernet2.46 | P2P_LINK_TO_R1-SITE1-WAN2_Ethernet1/5.46_vrf_customer46 | l3dot1q | - | 10.1.1.130/31 | customer46 | 9214 | False | - | - |
| Ethernet2.47 | P2P_LINK_TO_R1-SITE1-WAN2_Ethernet1/5.47_vrf_customer47 | l3dot1q | - | 10.1.1.130/31 | customer47 | 9214 | False | - | - |
| Ethernet2.48 | P2P_LINK_TO_R1-SITE1-WAN2_Ethernet1/5.48_vrf_customer48 | l3dot1q | - | 10.1.1.130/31 | customer48 | 9214 | False | - | - |
| Ethernet2.49 | P2P_LINK_TO_R1-SITE1-WAN2_Ethernet1/5.49_vrf_customer49 | l3dot1q | - | 10.1.1.130/31 | customer49 | 9214 | False | - | - |
| Ethernet2.50 | P2P_LINK_TO_R1-SITE1-WAN2_Ethernet1/5.50_vrf_customer50 | l3dot1q | - | 10.1.1.130/31 | customer50 | 9214 | False | - | - |
| Ethernet2.51 | P2P_LINK_TO_R1-SITE1-WAN2_Ethernet1/5.51_vrf_customer51 | l3dot1q | - | 10.1.1.130/31 | customer51 | 9214 | False | - | - |
| Ethernet2.52 | P2P_LINK_TO_R1-SITE1-WAN2_Ethernet1/5.52_vrf_customer52 | l3dot1q | - | 10.1.1.130/31 | customer52 | 9214 | False | - | - |
| Ethernet2.53 | P2P_LINK_TO_R1-SITE1-WAN2_Ethernet1/5.53_vrf_customer53 | l3dot1q | - | 10.1.1.130/31 | customer53 | 9214 | False | - | - |
| Ethernet2.54 | P2P_LINK_TO_R1-SITE1-WAN2_Ethernet1/5.54_vrf_customer54 | l3dot1q | - | 10.1.1.130/31 | customer54 | 9214 | False | - | - |
| Ethernet2.55 | P2P_LINK_TO_R1-SITE1-WAN2_Ethernet1/5.55_vrf_customer55 | l3dot1q | - | 10.1.1.130/31 | customer55 | 9214 | False | - | - |
| Ethernet2.56 | P2P_LINK_TO_R1-SITE1-WAN2_Ethernet1/5.56_vrf_customer56 | l3dot1q | - | 10.1.1.130/31 | customer56 | 9214 | False | - | - |
| Ethernet2.57 | P2P_LINK_TO_R1-SITE1-WAN2_Ethernet1/5.57_vrf_customer57 | l3dot1q | - | 10.1.1.130/31 | customer57 | 9214 | False | - | - |
| Ethernet2.58 | P2P_LINK_TO_R1-SITE1-WAN2_Ethernet1/5.58_vrf_customer58 | l3dot1q | - | 10.1.1.130/31 | customer58 | 9214 | False | - | - |
| Ethernet2.59 | P2P_LINK_TO_R1-SITE1-WAN2_Ethernet1/5.59_vrf_customer59 | l3dot1q | - | 10.1.1.130/31 | customer59 | 9214 | False | - | - |
| Ethernet2.60 | P2P_LINK_TO_R1-SITE1-WAN2_Ethernet1/5.60_vrf_customer60 | l3dot1q | - | 10.1.1.130/31 | customer60 | 9214 | False | - | - |
| Ethernet2.61 | P2P_LINK_TO_R1-SITE1-WAN2_Ethernet1/5.61_vrf_customer61 | l3dot1q | - | 10.1.1.130/31 | customer61 | 9214 | False | - | - |
| Ethernet2.62 | P2P_LINK_TO_R1-SITE1-WAN2_Ethernet1/5.62_vrf_customer62 | l3dot1q | - | 10.1.1.130/31 | customer62 | 9214 | False | - | - |
| Ethernet2.63 | P2P_LINK_TO_R1-SITE1-WAN2_Ethernet1/5.63_vrf_customer63 | l3dot1q | - | 10.1.1.130/31 | customer63 | 9214 | False | - | - |
| Ethernet2.64 | P2P_LINK_TO_R1-SITE1-WAN2_Ethernet1/5.64_vrf_customer64 | l3dot1q | - | 10.1.1.130/31 | customer64 | 9214 | False | - | - |
| Ethernet2.254 | P2P_LINK_TO_R1-SITE1-WAN2_Ethernet1/5.254_vrf_customer1 | l3dot1q | - | 10.1.1.130/31 | customer1 | 9214 | False | - | - |

#### Ethernet Interfaces Device Configuration

```eos
!
interface Ethernet1
   description P2P_LINK_TO_R1-SITE1-WAN1_Ethernet1/5
   no shutdown
   mtu 9214
   speed 10g
   no switchport
   ip address 10.1.1.128/31
!
interface Ethernet1.2
   description P2P_LINK_TO_R1-SITE1-WAN1_Ethernet1/5.2_vrf_customer2
   no shutdown
   mtu 9214
   encapsulation dot1q vlan 2
   vrf customer2
   ip address 10.1.1.128/31
!
interface Ethernet1.3
   description P2P_LINK_TO_R1-SITE1-WAN1_Ethernet1/5.3_vrf_customer3
   no shutdown
   mtu 9214
   encapsulation dot1q vlan 3
   vrf customer3
   ip address 10.1.1.128/31
!
interface Ethernet1.4
   description P2P_LINK_TO_R1-SITE1-WAN1_Ethernet1/5.4_vrf_customer4
   no shutdown
   mtu 9214
   encapsulation dot1q vlan 4
   vrf customer4
   ip address 10.1.1.128/31
!
interface Ethernet1.5
   description P2P_LINK_TO_R1-SITE1-WAN1_Ethernet1/5.5_vrf_customer5
   no shutdown
   mtu 9214
   encapsulation dot1q vlan 5
   vrf customer5
   ip address 10.1.1.128/31
!
interface Ethernet1.6
   description P2P_LINK_TO_R1-SITE1-WAN1_Ethernet1/5.6_vrf_customer6
   no shutdown
   mtu 9214
   encapsulation dot1q vlan 6
   vrf customer6
   ip address 10.1.1.128/31
!
interface Ethernet1.7
   description P2P_LINK_TO_R1-SITE1-WAN1_Ethernet1/5.7_vrf_customer7
   no shutdown
   mtu 9214
   encapsulation dot1q vlan 7
   vrf customer7
   ip address 10.1.1.128/31
!
interface Ethernet1.8
   description P2P_LINK_TO_R1-SITE1-WAN1_Ethernet1/5.8_vrf_customer8
   no shutdown
   mtu 9214
   encapsulation dot1q vlan 8
   vrf customer8
   ip address 10.1.1.128/31
!
interface Ethernet1.9
   description P2P_LINK_TO_R1-SITE1-WAN1_Ethernet1/5.9_vrf_customer9
   no shutdown
   mtu 9214
   encapsulation dot1q vlan 9
   vrf customer9
   ip address 10.1.1.128/31
!
interface Ethernet1.10
   description P2P_LINK_TO_R1-SITE1-WAN1_Ethernet1/5.10_vrf_customer10
   no shutdown
   mtu 9214
   encapsulation dot1q vlan 10
   vrf customer10
   ip address 10.1.1.128/31
!
interface Ethernet1.11
   description P2P_LINK_TO_R1-SITE1-WAN1_Ethernet1/5.11_vrf_customer11
   no shutdown
   mtu 9214
   encapsulation dot1q vlan 11
   vrf customer11
   ip address 10.1.1.128/31
!
interface Ethernet1.12
   description P2P_LINK_TO_R1-SITE1-WAN1_Ethernet1/5.12_vrf_customer12
   no shutdown
   mtu 9214
   encapsulation dot1q vlan 12
   vrf customer12
   ip address 10.1.1.128/31
!
interface Ethernet1.13
   description P2P_LINK_TO_R1-SITE1-WAN1_Ethernet1/5.13_vrf_customer13
   no shutdown
   mtu 9214
   encapsulation dot1q vlan 13
   vrf customer13
   ip address 10.1.1.128/31
!
interface Ethernet1.14
   description P2P_LINK_TO_R1-SITE1-WAN1_Ethernet1/5.14_vrf_customer14
   no shutdown
   mtu 9214
   encapsulation dot1q vlan 14
   vrf customer14
   ip address 10.1.1.128/31
!
interface Ethernet1.15
   description P2P_LINK_TO_R1-SITE1-WAN1_Ethernet1/5.15_vrf_customer15
   no shutdown
   mtu 9214
   encapsulation dot1q vlan 15
   vrf customer15
   ip address 10.1.1.128/31
!
interface Ethernet1.16
   description P2P_LINK_TO_R1-SITE1-WAN1_Ethernet1/5.16_vrf_customer16
   no shutdown
   mtu 9214
   encapsulation dot1q vlan 16
   vrf customer16
   ip address 10.1.1.128/31
!
interface Ethernet1.17
   description P2P_LINK_TO_R1-SITE1-WAN1_Ethernet1/5.17_vrf_customer17
   no shutdown
   mtu 9214
   encapsulation dot1q vlan 17
   vrf customer17
   ip address 10.1.1.128/31
!
interface Ethernet1.18
   description P2P_LINK_TO_R1-SITE1-WAN1_Ethernet1/5.18_vrf_customer18
   no shutdown
   mtu 9214
   encapsulation dot1q vlan 18
   vrf customer18
   ip address 10.1.1.128/31
!
interface Ethernet1.19
   description P2P_LINK_TO_R1-SITE1-WAN1_Ethernet1/5.19_vrf_customer19
   no shutdown
   mtu 9214
   encapsulation dot1q vlan 19
   vrf customer19
   ip address 10.1.1.128/31
!
interface Ethernet1.20
   description P2P_LINK_TO_R1-SITE1-WAN1_Ethernet1/5.20_vrf_customer20
   no shutdown
   mtu 9214
   encapsulation dot1q vlan 20
   vrf customer20
   ip address 10.1.1.128/31
!
interface Ethernet1.21
   description P2P_LINK_TO_R1-SITE1-WAN1_Ethernet1/5.21_vrf_customer21
   no shutdown
   mtu 9214
   encapsulation dot1q vlan 21
   vrf customer21
   ip address 10.1.1.128/31
!
interface Ethernet1.22
   description P2P_LINK_TO_R1-SITE1-WAN1_Ethernet1/5.22_vrf_customer22
   no shutdown
   mtu 9214
   encapsulation dot1q vlan 22
   vrf customer22
   ip address 10.1.1.128/31
!
interface Ethernet1.23
   description P2P_LINK_TO_R1-SITE1-WAN1_Ethernet1/5.23_vrf_customer23
   no shutdown
   mtu 9214
   encapsulation dot1q vlan 23
   vrf customer23
   ip address 10.1.1.128/31
!
interface Ethernet1.24
   description P2P_LINK_TO_R1-SITE1-WAN1_Ethernet1/5.24_vrf_customer24
   no shutdown
   mtu 9214
   encapsulation dot1q vlan 24
   vrf customer24
   ip address 10.1.1.128/31
!
interface Ethernet1.25
   description P2P_LINK_TO_R1-SITE1-WAN1_Ethernet1/5.25_vrf_customer25
   no shutdown
   mtu 9214
   encapsulation dot1q vlan 25
   vrf customer25
   ip address 10.1.1.128/31
!
interface Ethernet1.26
   description P2P_LINK_TO_R1-SITE1-WAN1_Ethernet1/5.26_vrf_customer26
   no shutdown
   mtu 9214
   encapsulation dot1q vlan 26
   vrf customer26
   ip address 10.1.1.128/31
!
interface Ethernet1.27
   description P2P_LINK_TO_R1-SITE1-WAN1_Ethernet1/5.27_vrf_customer27
   no shutdown
   mtu 9214
   encapsulation dot1q vlan 27
   vrf customer27
   ip address 10.1.1.128/31
!
interface Ethernet1.28
   description P2P_LINK_TO_R1-SITE1-WAN1_Ethernet1/5.28_vrf_customer28
   no shutdown
   mtu 9214
   encapsulation dot1q vlan 28
   vrf customer28
   ip address 10.1.1.128/31
!
interface Ethernet1.29
   description P2P_LINK_TO_R1-SITE1-WAN1_Ethernet1/5.29_vrf_customer29
   no shutdown
   mtu 9214
   encapsulation dot1q vlan 29
   vrf customer29
   ip address 10.1.1.128/31
!
interface Ethernet1.30
   description P2P_LINK_TO_R1-SITE1-WAN1_Ethernet1/5.30_vrf_customer30
   no shutdown
   mtu 9214
   encapsulation dot1q vlan 30
   vrf customer30
   ip address 10.1.1.128/31
!
interface Ethernet1.31
   description P2P_LINK_TO_R1-SITE1-WAN1_Ethernet1/5.31_vrf_customer31
   no shutdown
   mtu 9214
   encapsulation dot1q vlan 31
   vrf customer31
   ip address 10.1.1.128/31
!
interface Ethernet1.32
   description P2P_LINK_TO_R1-SITE1-WAN1_Ethernet1/5.32_vrf_customer32
   no shutdown
   mtu 9214
   encapsulation dot1q vlan 32
   vrf customer32
   ip address 10.1.1.128/31
!
interface Ethernet1.33
   description P2P_LINK_TO_R1-SITE1-WAN1_Ethernet1/5.33_vrf_customer33
   no shutdown
   mtu 9214
   encapsulation dot1q vlan 33
   vrf customer33
   ip address 10.1.1.128/31
!
interface Ethernet1.34
   description P2P_LINK_TO_R1-SITE1-WAN1_Ethernet1/5.34_vrf_customer34
   no shutdown
   mtu 9214
   encapsulation dot1q vlan 34
   vrf customer34
   ip address 10.1.1.128/31
!
interface Ethernet1.35
   description P2P_LINK_TO_R1-SITE1-WAN1_Ethernet1/5.35_vrf_customer35
   no shutdown
   mtu 9214
   encapsulation dot1q vlan 35
   vrf customer35
   ip address 10.1.1.128/31
!
interface Ethernet1.36
   description P2P_LINK_TO_R1-SITE1-WAN1_Ethernet1/5.36_vrf_customer36
   no shutdown
   mtu 9214
   encapsulation dot1q vlan 36
   vrf customer36
   ip address 10.1.1.128/31
!
interface Ethernet1.37
   description P2P_LINK_TO_R1-SITE1-WAN1_Ethernet1/5.37_vrf_customer37
   no shutdown
   mtu 9214
   encapsulation dot1q vlan 37
   vrf customer37
   ip address 10.1.1.128/31
!
interface Ethernet1.38
   description P2P_LINK_TO_R1-SITE1-WAN1_Ethernet1/5.38_vrf_customer38
   no shutdown
   mtu 9214
   encapsulation dot1q vlan 38
   vrf customer38
   ip address 10.1.1.128/31
!
interface Ethernet1.39
   description P2P_LINK_TO_R1-SITE1-WAN1_Ethernet1/5.39_vrf_customer39
   no shutdown
   mtu 9214
   encapsulation dot1q vlan 39
   vrf customer39
   ip address 10.1.1.128/31
!
interface Ethernet1.40
   description P2P_LINK_TO_R1-SITE1-WAN1_Ethernet1/5.40_vrf_customer40
   no shutdown
   mtu 9214
   encapsulation dot1q vlan 40
   vrf customer40
   ip address 10.1.1.128/31
!
interface Ethernet1.41
   description P2P_LINK_TO_R1-SITE1-WAN1_Ethernet1/5.41_vrf_customer41
   no shutdown
   mtu 9214
   encapsulation dot1q vlan 41
   vrf customer41
   ip address 10.1.1.128/31
!
interface Ethernet1.42
   description P2P_LINK_TO_R1-SITE1-WAN1_Ethernet1/5.42_vrf_customer42
   no shutdown
   mtu 9214
   encapsulation dot1q vlan 42
   vrf customer42
   ip address 10.1.1.128/31
!
interface Ethernet1.43
   description P2P_LINK_TO_R1-SITE1-WAN1_Ethernet1/5.43_vrf_customer43
   no shutdown
   mtu 9214
   encapsulation dot1q vlan 43
   vrf customer43
   ip address 10.1.1.128/31
!
interface Ethernet1.44
   description P2P_LINK_TO_R1-SITE1-WAN1_Ethernet1/5.44_vrf_customer44
   no shutdown
   mtu 9214
   encapsulation dot1q vlan 44
   vrf customer44
   ip address 10.1.1.128/31
!
interface Ethernet1.45
   description P2P_LINK_TO_R1-SITE1-WAN1_Ethernet1/5.45_vrf_customer45
   no shutdown
   mtu 9214
   encapsulation dot1q vlan 45
   vrf customer45
   ip address 10.1.1.128/31
!
interface Ethernet1.46
   description P2P_LINK_TO_R1-SITE1-WAN1_Ethernet1/5.46_vrf_customer46
   no shutdown
   mtu 9214
   encapsulation dot1q vlan 46
   vrf customer46
   ip address 10.1.1.128/31
!
interface Ethernet1.47
   description P2P_LINK_TO_R1-SITE1-WAN1_Ethernet1/5.47_vrf_customer47
   no shutdown
   mtu 9214
   encapsulation dot1q vlan 47
   vrf customer47
   ip address 10.1.1.128/31
!
interface Ethernet1.48
   description P2P_LINK_TO_R1-SITE1-WAN1_Ethernet1/5.48_vrf_customer48
   no shutdown
   mtu 9214
   encapsulation dot1q vlan 48
   vrf customer48
   ip address 10.1.1.128/31
!
interface Ethernet1.49
   description P2P_LINK_TO_R1-SITE1-WAN1_Ethernet1/5.49_vrf_customer49
   no shutdown
   mtu 9214
   encapsulation dot1q vlan 49
   vrf customer49
   ip address 10.1.1.128/31
!
interface Ethernet1.50
   description P2P_LINK_TO_R1-SITE1-WAN1_Ethernet1/5.50_vrf_customer50
   no shutdown
   mtu 9214
   encapsulation dot1q vlan 50
   vrf customer50
   ip address 10.1.1.128/31
!
interface Ethernet1.51
   description P2P_LINK_TO_R1-SITE1-WAN1_Ethernet1/5.51_vrf_customer51
   no shutdown
   mtu 9214
   encapsulation dot1q vlan 51
   vrf customer51
   ip address 10.1.1.128/31
!
interface Ethernet1.52
   description P2P_LINK_TO_R1-SITE1-WAN1_Ethernet1/5.52_vrf_customer52
   no shutdown
   mtu 9214
   encapsulation dot1q vlan 52
   vrf customer52
   ip address 10.1.1.128/31
!
interface Ethernet1.53
   description P2P_LINK_TO_R1-SITE1-WAN1_Ethernet1/5.53_vrf_customer53
   no shutdown
   mtu 9214
   encapsulation dot1q vlan 53
   vrf customer53
   ip address 10.1.1.128/31
!
interface Ethernet1.54
   description P2P_LINK_TO_R1-SITE1-WAN1_Ethernet1/5.54_vrf_customer54
   no shutdown
   mtu 9214
   encapsulation dot1q vlan 54
   vrf customer54
   ip address 10.1.1.128/31
!
interface Ethernet1.55
   description P2P_LINK_TO_R1-SITE1-WAN1_Ethernet1/5.55_vrf_customer55
   no shutdown
   mtu 9214
   encapsulation dot1q vlan 55
   vrf customer55
   ip address 10.1.1.128/31
!
interface Ethernet1.56
   description P2P_LINK_TO_R1-SITE1-WAN1_Ethernet1/5.56_vrf_customer56
   no shutdown
   mtu 9214
   encapsulation dot1q vlan 56
   vrf customer56
   ip address 10.1.1.128/31
!
interface Ethernet1.57
   description P2P_LINK_TO_R1-SITE1-WAN1_Ethernet1/5.57_vrf_customer57
   no shutdown
   mtu 9214
   encapsulation dot1q vlan 57
   vrf customer57
   ip address 10.1.1.128/31
!
interface Ethernet1.58
   description P2P_LINK_TO_R1-SITE1-WAN1_Ethernet1/5.58_vrf_customer58
   no shutdown
   mtu 9214
   encapsulation dot1q vlan 58
   vrf customer58
   ip address 10.1.1.128/31
!
interface Ethernet1.59
   description P2P_LINK_TO_R1-SITE1-WAN1_Ethernet1/5.59_vrf_customer59
   no shutdown
   mtu 9214
   encapsulation dot1q vlan 59
   vrf customer59
   ip address 10.1.1.128/31
!
interface Ethernet1.60
   description P2P_LINK_TO_R1-SITE1-WAN1_Ethernet1/5.60_vrf_customer60
   no shutdown
   mtu 9214
   encapsulation dot1q vlan 60
   vrf customer60
   ip address 10.1.1.128/31
!
interface Ethernet1.61
   description P2P_LINK_TO_R1-SITE1-WAN1_Ethernet1/5.61_vrf_customer61
   no shutdown
   mtu 9214
   encapsulation dot1q vlan 61
   vrf customer61
   ip address 10.1.1.128/31
!
interface Ethernet1.62
   description P2P_LINK_TO_R1-SITE1-WAN1_Ethernet1/5.62_vrf_customer62
   no shutdown
   mtu 9214
   encapsulation dot1q vlan 62
   vrf customer62
   ip address 10.1.1.128/31
!
interface Ethernet1.63
   description P2P_LINK_TO_R1-SITE1-WAN1_Ethernet1/5.63_vrf_customer63
   no shutdown
   mtu 9214
   encapsulation dot1q vlan 63
   vrf customer63
   ip address 10.1.1.128/31
!
interface Ethernet1.64
   description P2P_LINK_TO_R1-SITE1-WAN1_Ethernet1/5.64_vrf_customer64
   no shutdown
   mtu 9214
   encapsulation dot1q vlan 64
   vrf customer64
   ip address 10.1.1.128/31
!
interface Ethernet1.254
   description P2P_LINK_TO_R1-SITE1-WAN1_Ethernet1/5.254_vrf_customer1
   no shutdown
   mtu 9214
   encapsulation dot1q vlan 254
   vrf customer1
   ip address 10.1.1.128/31
!
interface Ethernet2
   description P2P_LINK_TO_R1-SITE1-WAN2_Ethernet1/5
   no shutdown
   mtu 9214
   speed 10g
   no switchport
   ip address 10.1.1.130/31
!
interface Ethernet2.2
   description P2P_LINK_TO_R1-SITE1-WAN2_Ethernet1/5.2_vrf_customer2
   no shutdown
   mtu 9214
   encapsulation dot1q vlan 2
   vrf customer2
   ip address 10.1.1.130/31
!
interface Ethernet2.3
   description P2P_LINK_TO_R1-SITE1-WAN2_Ethernet1/5.3_vrf_customer3
   no shutdown
   mtu 9214
   encapsulation dot1q vlan 3
   vrf customer3
   ip address 10.1.1.130/31
!
interface Ethernet2.4
   description P2P_LINK_TO_R1-SITE1-WAN2_Ethernet1/5.4_vrf_customer4
   no shutdown
   mtu 9214
   encapsulation dot1q vlan 4
   vrf customer4
   ip address 10.1.1.130/31
!
interface Ethernet2.5
   description P2P_LINK_TO_R1-SITE1-WAN2_Ethernet1/5.5_vrf_customer5
   no shutdown
   mtu 9214
   encapsulation dot1q vlan 5
   vrf customer5
   ip address 10.1.1.130/31
!
interface Ethernet2.6
   description P2P_LINK_TO_R1-SITE1-WAN2_Ethernet1/5.6_vrf_customer6
   no shutdown
   mtu 9214
   encapsulation dot1q vlan 6
   vrf customer6
   ip address 10.1.1.130/31
!
interface Ethernet2.7
   description P2P_LINK_TO_R1-SITE1-WAN2_Ethernet1/5.7_vrf_customer7
   no shutdown
   mtu 9214
   encapsulation dot1q vlan 7
   vrf customer7
   ip address 10.1.1.130/31
!
interface Ethernet2.8
   description P2P_LINK_TO_R1-SITE1-WAN2_Ethernet1/5.8_vrf_customer8
   no shutdown
   mtu 9214
   encapsulation dot1q vlan 8
   vrf customer8
   ip address 10.1.1.130/31
!
interface Ethernet2.9
   description P2P_LINK_TO_R1-SITE1-WAN2_Ethernet1/5.9_vrf_customer9
   no shutdown
   mtu 9214
   encapsulation dot1q vlan 9
   vrf customer9
   ip address 10.1.1.130/31
!
interface Ethernet2.10
   description P2P_LINK_TO_R1-SITE1-WAN2_Ethernet1/5.10_vrf_customer10
   no shutdown
   mtu 9214
   encapsulation dot1q vlan 10
   vrf customer10
   ip address 10.1.1.130/31
!
interface Ethernet2.11
   description P2P_LINK_TO_R1-SITE1-WAN2_Ethernet1/5.11_vrf_customer11
   no shutdown
   mtu 9214
   encapsulation dot1q vlan 11
   vrf customer11
   ip address 10.1.1.130/31
!
interface Ethernet2.12
   description P2P_LINK_TO_R1-SITE1-WAN2_Ethernet1/5.12_vrf_customer12
   no shutdown
   mtu 9214
   encapsulation dot1q vlan 12
   vrf customer12
   ip address 10.1.1.130/31
!
interface Ethernet2.13
   description P2P_LINK_TO_R1-SITE1-WAN2_Ethernet1/5.13_vrf_customer13
   no shutdown
   mtu 9214
   encapsulation dot1q vlan 13
   vrf customer13
   ip address 10.1.1.130/31
!
interface Ethernet2.14
   description P2P_LINK_TO_R1-SITE1-WAN2_Ethernet1/5.14_vrf_customer14
   no shutdown
   mtu 9214
   encapsulation dot1q vlan 14
   vrf customer14
   ip address 10.1.1.130/31
!
interface Ethernet2.15
   description P2P_LINK_TO_R1-SITE1-WAN2_Ethernet1/5.15_vrf_customer15
   no shutdown
   mtu 9214
   encapsulation dot1q vlan 15
   vrf customer15
   ip address 10.1.1.130/31
!
interface Ethernet2.16
   description P2P_LINK_TO_R1-SITE1-WAN2_Ethernet1/5.16_vrf_customer16
   no shutdown
   mtu 9214
   encapsulation dot1q vlan 16
   vrf customer16
   ip address 10.1.1.130/31
!
interface Ethernet2.17
   description P2P_LINK_TO_R1-SITE1-WAN2_Ethernet1/5.17_vrf_customer17
   no shutdown
   mtu 9214
   encapsulation dot1q vlan 17
   vrf customer17
   ip address 10.1.1.130/31
!
interface Ethernet2.18
   description P2P_LINK_TO_R1-SITE1-WAN2_Ethernet1/5.18_vrf_customer18
   no shutdown
   mtu 9214
   encapsulation dot1q vlan 18
   vrf customer18
   ip address 10.1.1.130/31
!
interface Ethernet2.19
   description P2P_LINK_TO_R1-SITE1-WAN2_Ethernet1/5.19_vrf_customer19
   no shutdown
   mtu 9214
   encapsulation dot1q vlan 19
   vrf customer19
   ip address 10.1.1.130/31
!
interface Ethernet2.20
   description P2P_LINK_TO_R1-SITE1-WAN2_Ethernet1/5.20_vrf_customer20
   no shutdown
   mtu 9214
   encapsulation dot1q vlan 20
   vrf customer20
   ip address 10.1.1.130/31
!
interface Ethernet2.21
   description P2P_LINK_TO_R1-SITE1-WAN2_Ethernet1/5.21_vrf_customer21
   no shutdown
   mtu 9214
   encapsulation dot1q vlan 21
   vrf customer21
   ip address 10.1.1.130/31
!
interface Ethernet2.22
   description P2P_LINK_TO_R1-SITE1-WAN2_Ethernet1/5.22_vrf_customer22
   no shutdown
   mtu 9214
   encapsulation dot1q vlan 22
   vrf customer22
   ip address 10.1.1.130/31
!
interface Ethernet2.23
   description P2P_LINK_TO_R1-SITE1-WAN2_Ethernet1/5.23_vrf_customer23
   no shutdown
   mtu 9214
   encapsulation dot1q vlan 23
   vrf customer23
   ip address 10.1.1.130/31
!
interface Ethernet2.24
   description P2P_LINK_TO_R1-SITE1-WAN2_Ethernet1/5.24_vrf_customer24
   no shutdown
   mtu 9214
   encapsulation dot1q vlan 24
   vrf customer24
   ip address 10.1.1.130/31
!
interface Ethernet2.25
   description P2P_LINK_TO_R1-SITE1-WAN2_Ethernet1/5.25_vrf_customer25
   no shutdown
   mtu 9214
   encapsulation dot1q vlan 25
   vrf customer25
   ip address 10.1.1.130/31
!
interface Ethernet2.26
   description P2P_LINK_TO_R1-SITE1-WAN2_Ethernet1/5.26_vrf_customer26
   no shutdown
   mtu 9214
   encapsulation dot1q vlan 26
   vrf customer26
   ip address 10.1.1.130/31
!
interface Ethernet2.27
   description P2P_LINK_TO_R1-SITE1-WAN2_Ethernet1/5.27_vrf_customer27
   no shutdown
   mtu 9214
   encapsulation dot1q vlan 27
   vrf customer27
   ip address 10.1.1.130/31
!
interface Ethernet2.28
   description P2P_LINK_TO_R1-SITE1-WAN2_Ethernet1/5.28_vrf_customer28
   no shutdown
   mtu 9214
   encapsulation dot1q vlan 28
   vrf customer28
   ip address 10.1.1.130/31
!
interface Ethernet2.29
   description P2P_LINK_TO_R1-SITE1-WAN2_Ethernet1/5.29_vrf_customer29
   no shutdown
   mtu 9214
   encapsulation dot1q vlan 29
   vrf customer29
   ip address 10.1.1.130/31
!
interface Ethernet2.30
   description P2P_LINK_TO_R1-SITE1-WAN2_Ethernet1/5.30_vrf_customer30
   no shutdown
   mtu 9214
   encapsulation dot1q vlan 30
   vrf customer30
   ip address 10.1.1.130/31
!
interface Ethernet2.31
   description P2P_LINK_TO_R1-SITE1-WAN2_Ethernet1/5.31_vrf_customer31
   no shutdown
   mtu 9214
   encapsulation dot1q vlan 31
   vrf customer31
   ip address 10.1.1.130/31
!
interface Ethernet2.32
   description P2P_LINK_TO_R1-SITE1-WAN2_Ethernet1/5.32_vrf_customer32
   no shutdown
   mtu 9214
   encapsulation dot1q vlan 32
   vrf customer32
   ip address 10.1.1.130/31
!
interface Ethernet2.33
   description P2P_LINK_TO_R1-SITE1-WAN2_Ethernet1/5.33_vrf_customer33
   no shutdown
   mtu 9214
   encapsulation dot1q vlan 33
   vrf customer33
   ip address 10.1.1.130/31
!
interface Ethernet2.34
   description P2P_LINK_TO_R1-SITE1-WAN2_Ethernet1/5.34_vrf_customer34
   no shutdown
   mtu 9214
   encapsulation dot1q vlan 34
   vrf customer34
   ip address 10.1.1.130/31
!
interface Ethernet2.35
   description P2P_LINK_TO_R1-SITE1-WAN2_Ethernet1/5.35_vrf_customer35
   no shutdown
   mtu 9214
   encapsulation dot1q vlan 35
   vrf customer35
   ip address 10.1.1.130/31
!
interface Ethernet2.36
   description P2P_LINK_TO_R1-SITE1-WAN2_Ethernet1/5.36_vrf_customer36
   no shutdown
   mtu 9214
   encapsulation dot1q vlan 36
   vrf customer36
   ip address 10.1.1.130/31
!
interface Ethernet2.37
   description P2P_LINK_TO_R1-SITE1-WAN2_Ethernet1/5.37_vrf_customer37
   no shutdown
   mtu 9214
   encapsulation dot1q vlan 37
   vrf customer37
   ip address 10.1.1.130/31
!
interface Ethernet2.38
   description P2P_LINK_TO_R1-SITE1-WAN2_Ethernet1/5.38_vrf_customer38
   no shutdown
   mtu 9214
   encapsulation dot1q vlan 38
   vrf customer38
   ip address 10.1.1.130/31
!
interface Ethernet2.39
   description P2P_LINK_TO_R1-SITE1-WAN2_Ethernet1/5.39_vrf_customer39
   no shutdown
   mtu 9214
   encapsulation dot1q vlan 39
   vrf customer39
   ip address 10.1.1.130/31
!
interface Ethernet2.40
   description P2P_LINK_TO_R1-SITE1-WAN2_Ethernet1/5.40_vrf_customer40
   no shutdown
   mtu 9214
   encapsulation dot1q vlan 40
   vrf customer40
   ip address 10.1.1.130/31
!
interface Ethernet2.41
   description P2P_LINK_TO_R1-SITE1-WAN2_Ethernet1/5.41_vrf_customer41
   no shutdown
   mtu 9214
   encapsulation dot1q vlan 41
   vrf customer41
   ip address 10.1.1.130/31
!
interface Ethernet2.42
   description P2P_LINK_TO_R1-SITE1-WAN2_Ethernet1/5.42_vrf_customer42
   no shutdown
   mtu 9214
   encapsulation dot1q vlan 42
   vrf customer42
   ip address 10.1.1.130/31
!
interface Ethernet2.43
   description P2P_LINK_TO_R1-SITE1-WAN2_Ethernet1/5.43_vrf_customer43
   no shutdown
   mtu 9214
   encapsulation dot1q vlan 43
   vrf customer43
   ip address 10.1.1.130/31
!
interface Ethernet2.44
   description P2P_LINK_TO_R1-SITE1-WAN2_Ethernet1/5.44_vrf_customer44
   no shutdown
   mtu 9214
   encapsulation dot1q vlan 44
   vrf customer44
   ip address 10.1.1.130/31
!
interface Ethernet2.45
   description P2P_LINK_TO_R1-SITE1-WAN2_Ethernet1/5.45_vrf_customer45
   no shutdown
   mtu 9214
   encapsulation dot1q vlan 45
   vrf customer45
   ip address 10.1.1.130/31
!
interface Ethernet2.46
   description P2P_LINK_TO_R1-SITE1-WAN2_Ethernet1/5.46_vrf_customer46
   no shutdown
   mtu 9214
   encapsulation dot1q vlan 46
   vrf customer46
   ip address 10.1.1.130/31
!
interface Ethernet2.47
   description P2P_LINK_TO_R1-SITE1-WAN2_Ethernet1/5.47_vrf_customer47
   no shutdown
   mtu 9214
   encapsulation dot1q vlan 47
   vrf customer47
   ip address 10.1.1.130/31
!
interface Ethernet2.48
   description P2P_LINK_TO_R1-SITE1-WAN2_Ethernet1/5.48_vrf_customer48
   no shutdown
   mtu 9214
   encapsulation dot1q vlan 48
   vrf customer48
   ip address 10.1.1.130/31
!
interface Ethernet2.49
   description P2P_LINK_TO_R1-SITE1-WAN2_Ethernet1/5.49_vrf_customer49
   no shutdown
   mtu 9214
   encapsulation dot1q vlan 49
   vrf customer49
   ip address 10.1.1.130/31
!
interface Ethernet2.50
   description P2P_LINK_TO_R1-SITE1-WAN2_Ethernet1/5.50_vrf_customer50
   no shutdown
   mtu 9214
   encapsulation dot1q vlan 50
   vrf customer50
   ip address 10.1.1.130/31
!
interface Ethernet2.51
   description P2P_LINK_TO_R1-SITE1-WAN2_Ethernet1/5.51_vrf_customer51
   no shutdown
   mtu 9214
   encapsulation dot1q vlan 51
   vrf customer51
   ip address 10.1.1.130/31
!
interface Ethernet2.52
   description P2P_LINK_TO_R1-SITE1-WAN2_Ethernet1/5.52_vrf_customer52
   no shutdown
   mtu 9214
   encapsulation dot1q vlan 52
   vrf customer52
   ip address 10.1.1.130/31
!
interface Ethernet2.53
   description P2P_LINK_TO_R1-SITE1-WAN2_Ethernet1/5.53_vrf_customer53
   no shutdown
   mtu 9214
   encapsulation dot1q vlan 53
   vrf customer53
   ip address 10.1.1.130/31
!
interface Ethernet2.54
   description P2P_LINK_TO_R1-SITE1-WAN2_Ethernet1/5.54_vrf_customer54
   no shutdown
   mtu 9214
   encapsulation dot1q vlan 54
   vrf customer54
   ip address 10.1.1.130/31
!
interface Ethernet2.55
   description P2P_LINK_TO_R1-SITE1-WAN2_Ethernet1/5.55_vrf_customer55
   no shutdown
   mtu 9214
   encapsulation dot1q vlan 55
   vrf customer55
   ip address 10.1.1.130/31
!
interface Ethernet2.56
   description P2P_LINK_TO_R1-SITE1-WAN2_Ethernet1/5.56_vrf_customer56
   no shutdown
   mtu 9214
   encapsulation dot1q vlan 56
   vrf customer56
   ip address 10.1.1.130/31
!
interface Ethernet2.57
   description P2P_LINK_TO_R1-SITE1-WAN2_Ethernet1/5.57_vrf_customer57
   no shutdown
   mtu 9214
   encapsulation dot1q vlan 57
   vrf customer57
   ip address 10.1.1.130/31
!
interface Ethernet2.58
   description P2P_LINK_TO_R1-SITE1-WAN2_Ethernet1/5.58_vrf_customer58
   no shutdown
   mtu 9214
   encapsulation dot1q vlan 58
   vrf customer58
   ip address 10.1.1.130/31
!
interface Ethernet2.59
   description P2P_LINK_TO_R1-SITE1-WAN2_Ethernet1/5.59_vrf_customer59
   no shutdown
   mtu 9214
   encapsulation dot1q vlan 59
   vrf customer59
   ip address 10.1.1.130/31
!
interface Ethernet2.60
   description P2P_LINK_TO_R1-SITE1-WAN2_Ethernet1/5.60_vrf_customer60
   no shutdown
   mtu 9214
   encapsulation dot1q vlan 60
   vrf customer60
   ip address 10.1.1.130/31
!
interface Ethernet2.61
   description P2P_LINK_TO_R1-SITE1-WAN2_Ethernet1/5.61_vrf_customer61
   no shutdown
   mtu 9214
   encapsulation dot1q vlan 61
   vrf customer61
   ip address 10.1.1.130/31
!
interface Ethernet2.62
   description P2P_LINK_TO_R1-SITE1-WAN2_Ethernet1/5.62_vrf_customer62
   no shutdown
   mtu 9214
   encapsulation dot1q vlan 62
   vrf customer62
   ip address 10.1.1.130/31
!
interface Ethernet2.63
   description P2P_LINK_TO_R1-SITE1-WAN2_Ethernet1/5.63_vrf_customer63
   no shutdown
   mtu 9214
   encapsulation dot1q vlan 63
   vrf customer63
   ip address 10.1.1.130/31
!
interface Ethernet2.64
   description P2P_LINK_TO_R1-SITE1-WAN2_Ethernet1/5.64_vrf_customer64
   no shutdown
   mtu 9214
   encapsulation dot1q vlan 64
   vrf customer64
   ip address 10.1.1.130/31
!
interface Ethernet2.254
   description P2P_LINK_TO_R1-SITE1-WAN2_Ethernet1/5.254_vrf_customer1
   no shutdown
   mtu 9214
   encapsulation dot1q vlan 254
   vrf customer1
   ip address 10.1.1.130/31
!
interface Ethernet49/1
   description test-host2
   no shutdown
   switchport trunk allowed vlan 1-64,254
   switchport mode trunk
   switchport
```

### Loopback Interfaces

#### Loopback Interfaces Summary

##### IPv4

| Interface | Description | VRF | IP Address |
| --------- | ----------- | --- | ---------- |
| Loopback0 | EVPN_Overlay_Peering | default | 10.1.1.3/32 |
| Loopback1 | VTEP_VXLAN_Tunnel_Source | default | 10.1.1.67/32 |

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
   ip address 10.1.1.3/32
!
interface Loopback1
   description VTEP_VXLAN_Tunnel_Source
   no shutdown
   ip address 10.1.1.67/32
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
| Vlan7 | customer7 | customer7 | - | False |
| Vlan8 | customer8 | customer8 | - | False |
| Vlan9 | customer9 | customer9 | - | False |
| Vlan10 | customer10 | customer10 | - | False |
| Vlan11 | customer11 | customer11 | - | False |
| Vlan12 | customer12 | customer12 | - | False |
| Vlan13 | customer13 | customer13 | - | False |
| Vlan14 | customer14 | customer14 | - | False |
| Vlan15 | customer15 | customer15 | - | False |
| Vlan16 | customer16 | customer16 | - | False |
| Vlan17 | customer17 | customer17 | - | False |
| Vlan18 | customer18 | customer18 | - | False |
| Vlan19 | customer19 | customer19 | - | False |
| Vlan20 | customer20 | customer20 | - | False |
| Vlan21 | customer21 | customer21 | - | False |
| Vlan22 | customer22 | customer22 | - | False |
| Vlan23 | customer23 | customer23 | - | False |
| Vlan24 | customer24 | customer24 | - | False |
| Vlan25 | customer25 | customer25 | - | False |
| Vlan26 | customer26 | customer26 | - | False |
| Vlan27 | customer27 | customer27 | - | False |
| Vlan28 | customer28 | customer28 | - | False |
| Vlan29 | customer29 | customer29 | - | False |
| Vlan30 | customer30 | customer30 | - | False |
| Vlan31 | customer31 | customer31 | - | False |
| Vlan32 | customer32 | customer32 | - | False |
| Vlan33 | customer33 | customer33 | - | False |
| Vlan34 | customer34 | customer34 | - | False |
| Vlan35 | customer35 | customer35 | - | False |
| Vlan36 | customer36 | customer36 | - | False |
| Vlan37 | customer37 | customer37 | - | False |
| Vlan38 | customer38 | customer38 | - | False |
| Vlan39 | customer39 | customer39 | - | False |
| Vlan40 | customer40 | customer40 | - | False |
| Vlan41 | customer41 | customer41 | - | False |
| Vlan42 | customer42 | customer42 | - | False |
| Vlan43 | customer43 | customer43 | - | False |
| Vlan44 | customer44 | customer44 | - | False |
| Vlan45 | customer45 | customer45 | - | False |
| Vlan46 | customer46 | customer46 | - | False |
| Vlan47 | customer47 | customer47 | - | False |
| Vlan48 | customer48 | customer48 | - | False |
| Vlan49 | customer49 | customer49 | - | False |
| Vlan50 | customer50 | customer50 | - | False |
| Vlan51 | customer51 | customer51 | - | False |
| Vlan52 | customer52 | customer52 | - | False |
| Vlan53 | customer53 | customer53 | - | False |
| Vlan54 | customer54 | customer54 | - | False |
| Vlan55 | customer55 | customer55 | - | False |
| Vlan56 | customer56 | customer56 | - | False |
| Vlan57 | customer57 | customer57 | - | False |
| Vlan58 | customer58 | customer58 | - | False |
| Vlan59 | customer59 | customer59 | - | False |
| Vlan60 | customer60 | customer60 | - | False |
| Vlan61 | customer61 | customer61 | - | False |
| Vlan62 | customer62 | customer62 | - | False |
| Vlan63 | customer63 | customer63 | - | False |
| Vlan64 | customer64 | customer64 | - | False |
| Vlan254 | customer1 | customer1 | - | False |

##### IPv4

| Interface | VRF | IP Address | IP Address Virtual | IP Router Virtual Address | VRRP | ACL In | ACL Out |
| --------- | --- | ---------- | ------------------ | ------------------------- | ---- | ------ | ------- |
| Vlan2 |  customer2  |  10.11.2.1/24  |  -  |  -  |  -  |  -  |  -  |
| Vlan3 |  customer3  |  10.11.3.1/24  |  -  |  -  |  -  |  -  |  -  |
| Vlan4 |  customer4  |  10.11.4.1/24  |  -  |  -  |  -  |  -  |  -  |
| Vlan5 |  customer5  |  10.11.5.1/24  |  -  |  -  |  -  |  -  |  -  |
| Vlan6 |  customer6  |  10.11.6.1/24  |  -  |  -  |  -  |  -  |  -  |
| Vlan7 |  customer7  |  10.11.7.1/24  |  -  |  -  |  -  |  -  |  -  |
| Vlan8 |  customer8  |  10.11.8.1/24  |  -  |  -  |  -  |  -  |  -  |
| Vlan9 |  customer9  |  10.11.9.1/24  |  -  |  -  |  -  |  -  |  -  |
| Vlan10 |  customer10  |  10.11.10.1/24  |  -  |  -  |  -  |  -  |  -  |
| Vlan11 |  customer11  |  10.11.11.1/24  |  -  |  -  |  -  |  -  |  -  |
| Vlan12 |  customer12  |  10.11.12.1/24  |  -  |  -  |  -  |  -  |  -  |
| Vlan13 |  customer13  |  10.11.13.1/24  |  -  |  -  |  -  |  -  |  -  |
| Vlan14 |  customer14  |  10.11.14.1/24  |  -  |  -  |  -  |  -  |  -  |
| Vlan15 |  customer15  |  10.11.15.1/24  |  -  |  -  |  -  |  -  |  -  |
| Vlan16 |  customer16  |  10.11.16.1/24  |  -  |  -  |  -  |  -  |  -  |
| Vlan17 |  customer17  |  10.11.17.1/24  |  -  |  -  |  -  |  -  |  -  |
| Vlan18 |  customer18  |  10.11.18.1/24  |  -  |  -  |  -  |  -  |  -  |
| Vlan19 |  customer19  |  10.11.19.1/24  |  -  |  -  |  -  |  -  |  -  |
| Vlan20 |  customer20  |  10.11.20.1/24  |  -  |  -  |  -  |  -  |  -  |
| Vlan21 |  customer21  |  10.11.21.1/24  |  -  |  -  |  -  |  -  |  -  |
| Vlan22 |  customer22  |  10.11.22.1/24  |  -  |  -  |  -  |  -  |  -  |
| Vlan23 |  customer23  |  10.11.23.1/24  |  -  |  -  |  -  |  -  |  -  |
| Vlan24 |  customer24  |  10.11.24.1/24  |  -  |  -  |  -  |  -  |  -  |
| Vlan25 |  customer25  |  10.11.25.1/24  |  -  |  -  |  -  |  -  |  -  |
| Vlan26 |  customer26  |  10.11.26.1/24  |  -  |  -  |  -  |  -  |  -  |
| Vlan27 |  customer27  |  10.11.27.1/24  |  -  |  -  |  -  |  -  |  -  |
| Vlan28 |  customer28  |  10.11.28.1/24  |  -  |  -  |  -  |  -  |  -  |
| Vlan29 |  customer29  |  10.11.29.1/24  |  -  |  -  |  -  |  -  |  -  |
| Vlan30 |  customer30  |  10.11.30.1/24  |  -  |  -  |  -  |  -  |  -  |
| Vlan31 |  customer31  |  10.11.31.1/24  |  -  |  -  |  -  |  -  |  -  |
| Vlan32 |  customer32  |  10.11.32.1/24  |  -  |  -  |  -  |  -  |  -  |
| Vlan33 |  customer33  |  10.11.33.1/24  |  -  |  -  |  -  |  -  |  -  |
| Vlan34 |  customer34  |  10.11.34.1/24  |  -  |  -  |  -  |  -  |  -  |
| Vlan35 |  customer35  |  10.11.35.1/24  |  -  |  -  |  -  |  -  |  -  |
| Vlan36 |  customer36  |  10.11.36.1/24  |  -  |  -  |  -  |  -  |  -  |
| Vlan37 |  customer37  |  10.11.37.1/24  |  -  |  -  |  -  |  -  |  -  |
| Vlan38 |  customer38  |  10.11.38.1/24  |  -  |  -  |  -  |  -  |  -  |
| Vlan39 |  customer39  |  10.11.39.1/24  |  -  |  -  |  -  |  -  |  -  |
| Vlan40 |  customer40  |  10.11.40.1/24  |  -  |  -  |  -  |  -  |  -  |
| Vlan41 |  customer41  |  10.11.41.1/24  |  -  |  -  |  -  |  -  |  -  |
| Vlan42 |  customer42  |  10.11.42.1/24  |  -  |  -  |  -  |  -  |  -  |
| Vlan43 |  customer43  |  10.11.43.1/24  |  -  |  -  |  -  |  -  |  -  |
| Vlan44 |  customer44  |  10.11.44.1/24  |  -  |  -  |  -  |  -  |  -  |
| Vlan45 |  customer45  |  10.11.45.1/24  |  -  |  -  |  -  |  -  |  -  |
| Vlan46 |  customer46  |  10.11.46.1/24  |  -  |  -  |  -  |  -  |  -  |
| Vlan47 |  customer47  |  10.11.47.1/24  |  -  |  -  |  -  |  -  |  -  |
| Vlan48 |  customer48  |  10.11.48.1/24  |  -  |  -  |  -  |  -  |  -  |
| Vlan49 |  customer49  |  10.11.49.1/24  |  -  |  -  |  -  |  -  |  -  |
| Vlan50 |  customer50  |  10.11.50.1/24  |  -  |  -  |  -  |  -  |  -  |
| Vlan51 |  customer51  |  10.11.51.1/24  |  -  |  -  |  -  |  -  |  -  |
| Vlan52 |  customer52  |  10.11.52.1/24  |  -  |  -  |  -  |  -  |  -  |
| Vlan53 |  customer53  |  10.11.53.1/24  |  -  |  -  |  -  |  -  |  -  |
| Vlan54 |  customer54  |  10.11.54.1/24  |  -  |  -  |  -  |  -  |  -  |
| Vlan55 |  customer55  |  10.11.55.1/24  |  -  |  -  |  -  |  -  |  -  |
| Vlan56 |  customer56  |  10.11.56.1/24  |  -  |  -  |  -  |  -  |  -  |
| Vlan57 |  customer57  |  10.11.57.1/24  |  -  |  -  |  -  |  -  |  -  |
| Vlan58 |  customer58  |  10.11.58.1/24  |  -  |  -  |  -  |  -  |  -  |
| Vlan59 |  customer59  |  10.11.59.1/24  |  -  |  -  |  -  |  -  |  -  |
| Vlan60 |  customer60  |  10.11.60.1/24  |  -  |  -  |  -  |  -  |  -  |
| Vlan61 |  customer61  |  10.11.61.1/24  |  -  |  -  |  -  |  -  |  -  |
| Vlan62 |  customer62  |  10.11.62.1/24  |  -  |  -  |  -  |  -  |  -  |
| Vlan63 |  customer63  |  10.11.63.1/24  |  -  |  -  |  -  |  -  |  -  |
| Vlan64 |  customer64  |  10.11.64.1/24  |  -  |  -  |  -  |  -  |  -  |
| Vlan254 |  customer1  |  10.11.1.1/24  |  -  |  -  |  -  |  -  |  -  |

#### VLAN Interfaces Device Configuration

```eos
!
interface Vlan2
   description customer2
   no shutdown
   vrf customer2
   ip address 10.11.2.1/24
!
interface Vlan3
   description customer3
   no shutdown
   vrf customer3
   ip address 10.11.3.1/24
!
interface Vlan4
   description customer4
   no shutdown
   vrf customer4
   ip address 10.11.4.1/24
!
interface Vlan5
   description customer5
   no shutdown
   vrf customer5
   ip address 10.11.5.1/24
!
interface Vlan6
   description customer6
   no shutdown
   vrf customer6
   ip address 10.11.6.1/24
!
interface Vlan7
   description customer7
   no shutdown
   vrf customer7
   ip address 10.11.7.1/24
!
interface Vlan8
   description customer8
   no shutdown
   vrf customer8
   ip address 10.11.8.1/24
!
interface Vlan9
   description customer9
   no shutdown
   vrf customer9
   ip address 10.11.9.1/24
!
interface Vlan10
   description customer10
   no shutdown
   vrf customer10
   ip address 10.11.10.1/24
!
interface Vlan11
   description customer11
   no shutdown
   vrf customer11
   ip address 10.11.11.1/24
!
interface Vlan12
   description customer12
   no shutdown
   vrf customer12
   ip address 10.11.12.1/24
!
interface Vlan13
   description customer13
   no shutdown
   vrf customer13
   ip address 10.11.13.1/24
!
interface Vlan14
   description customer14
   no shutdown
   vrf customer14
   ip address 10.11.14.1/24
!
interface Vlan15
   description customer15
   no shutdown
   vrf customer15
   ip address 10.11.15.1/24
!
interface Vlan16
   description customer16
   no shutdown
   vrf customer16
   ip address 10.11.16.1/24
!
interface Vlan17
   description customer17
   no shutdown
   vrf customer17
   ip address 10.11.17.1/24
!
interface Vlan18
   description customer18
   no shutdown
   vrf customer18
   ip address 10.11.18.1/24
!
interface Vlan19
   description customer19
   no shutdown
   vrf customer19
   ip address 10.11.19.1/24
!
interface Vlan20
   description customer20
   no shutdown
   vrf customer20
   ip address 10.11.20.1/24
!
interface Vlan21
   description customer21
   no shutdown
   vrf customer21
   ip address 10.11.21.1/24
!
interface Vlan22
   description customer22
   no shutdown
   vrf customer22
   ip address 10.11.22.1/24
!
interface Vlan23
   description customer23
   no shutdown
   vrf customer23
   ip address 10.11.23.1/24
!
interface Vlan24
   description customer24
   no shutdown
   vrf customer24
   ip address 10.11.24.1/24
!
interface Vlan25
   description customer25
   no shutdown
   vrf customer25
   ip address 10.11.25.1/24
!
interface Vlan26
   description customer26
   no shutdown
   vrf customer26
   ip address 10.11.26.1/24
!
interface Vlan27
   description customer27
   no shutdown
   vrf customer27
   ip address 10.11.27.1/24
!
interface Vlan28
   description customer28
   no shutdown
   vrf customer28
   ip address 10.11.28.1/24
!
interface Vlan29
   description customer29
   no shutdown
   vrf customer29
   ip address 10.11.29.1/24
!
interface Vlan30
   description customer30
   no shutdown
   vrf customer30
   ip address 10.11.30.1/24
!
interface Vlan31
   description customer31
   no shutdown
   vrf customer31
   ip address 10.11.31.1/24
!
interface Vlan32
   description customer32
   no shutdown
   vrf customer32
   ip address 10.11.32.1/24
!
interface Vlan33
   description customer33
   no shutdown
   vrf customer33
   ip address 10.11.33.1/24
!
interface Vlan34
   description customer34
   no shutdown
   vrf customer34
   ip address 10.11.34.1/24
!
interface Vlan35
   description customer35
   no shutdown
   vrf customer35
   ip address 10.11.35.1/24
!
interface Vlan36
   description customer36
   no shutdown
   vrf customer36
   ip address 10.11.36.1/24
!
interface Vlan37
   description customer37
   no shutdown
   vrf customer37
   ip address 10.11.37.1/24
!
interface Vlan38
   description customer38
   no shutdown
   vrf customer38
   ip address 10.11.38.1/24
!
interface Vlan39
   description customer39
   no shutdown
   vrf customer39
   ip address 10.11.39.1/24
!
interface Vlan40
   description customer40
   no shutdown
   vrf customer40
   ip address 10.11.40.1/24
!
interface Vlan41
   description customer41
   no shutdown
   vrf customer41
   ip address 10.11.41.1/24
!
interface Vlan42
   description customer42
   no shutdown
   vrf customer42
   ip address 10.11.42.1/24
!
interface Vlan43
   description customer43
   no shutdown
   vrf customer43
   ip address 10.11.43.1/24
!
interface Vlan44
   description customer44
   no shutdown
   vrf customer44
   ip address 10.11.44.1/24
!
interface Vlan45
   description customer45
   no shutdown
   vrf customer45
   ip address 10.11.45.1/24
!
interface Vlan46
   description customer46
   no shutdown
   vrf customer46
   ip address 10.11.46.1/24
!
interface Vlan47
   description customer47
   no shutdown
   vrf customer47
   ip address 10.11.47.1/24
!
interface Vlan48
   description customer48
   no shutdown
   vrf customer48
   ip address 10.11.48.1/24
!
interface Vlan49
   description customer49
   no shutdown
   vrf customer49
   ip address 10.11.49.1/24
!
interface Vlan50
   description customer50
   no shutdown
   vrf customer50
   ip address 10.11.50.1/24
!
interface Vlan51
   description customer51
   no shutdown
   vrf customer51
   ip address 10.11.51.1/24
!
interface Vlan52
   description customer52
   no shutdown
   vrf customer52
   ip address 10.11.52.1/24
!
interface Vlan53
   description customer53
   no shutdown
   vrf customer53
   ip address 10.11.53.1/24
!
interface Vlan54
   description customer54
   no shutdown
   vrf customer54
   ip address 10.11.54.1/24
!
interface Vlan55
   description customer55
   no shutdown
   vrf customer55
   ip address 10.11.55.1/24
!
interface Vlan56
   description customer56
   no shutdown
   vrf customer56
   ip address 10.11.56.1/24
!
interface Vlan57
   description customer57
   no shutdown
   vrf customer57
   ip address 10.11.57.1/24
!
interface Vlan58
   description customer58
   no shutdown
   vrf customer58
   ip address 10.11.58.1/24
!
interface Vlan59
   description customer59
   no shutdown
   vrf customer59
   ip address 10.11.59.1/24
!
interface Vlan60
   description customer60
   no shutdown
   vrf customer60
   ip address 10.11.60.1/24
!
interface Vlan61
   description customer61
   no shutdown
   vrf customer61
   ip address 10.11.61.1/24
!
interface Vlan62
   description customer62
   no shutdown
   vrf customer62
   ip address 10.11.62.1/24
!
interface Vlan63
   description customer63
   no shutdown
   vrf customer63
   ip address 10.11.63.1/24
!
interface Vlan64
   description customer64
   no shutdown
   vrf customer64
   ip address 10.11.64.1/24
!
interface Vlan254
   description customer1
   no shutdown
   vrf customer1
   ip address 10.11.1.1/24
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
| 7 | 10007 | - | - |
| 8 | 10008 | - | - |
| 9 | 10009 | - | - |
| 10 | 10010 | - | - |
| 11 | 10011 | - | - |
| 12 | 10012 | - | - |
| 13 | 10013 | - | - |
| 14 | 10014 | - | - |
| 15 | 10015 | - | - |
| 16 | 10016 | - | - |
| 17 | 10017 | - | - |
| 18 | 10018 | - | - |
| 19 | 10019 | - | - |
| 20 | 10020 | - | - |
| 21 | 10021 | - | - |
| 22 | 10022 | - | - |
| 23 | 10023 | - | - |
| 24 | 10024 | - | - |
| 25 | 10025 | - | - |
| 26 | 10026 | - | - |
| 27 | 10027 | - | - |
| 28 | 10028 | - | - |
| 29 | 10029 | - | - |
| 30 | 10030 | - | - |
| 31 | 10031 | - | - |
| 32 | 10032 | - | - |
| 33 | 10033 | - | - |
| 34 | 10034 | - | - |
| 35 | 10035 | - | - |
| 36 | 10036 | - | - |
| 37 | 10037 | - | - |
| 38 | 10038 | - | - |
| 39 | 10039 | - | - |
| 40 | 10040 | - | - |
| 41 | 10041 | - | - |
| 42 | 10042 | - | - |
| 43 | 10043 | - | - |
| 44 | 10044 | - | - |
| 45 | 10045 | - | - |
| 46 | 10046 | - | - |
| 47 | 10047 | - | - |
| 48 | 10048 | - | - |
| 49 | 10049 | - | - |
| 50 | 10050 | - | - |
| 51 | 10051 | - | - |
| 52 | 10052 | - | - |
| 53 | 10053 | - | - |
| 54 | 10054 | - | - |
| 55 | 10055 | - | - |
| 56 | 10056 | - | - |
| 57 | 10057 | - | - |
| 58 | 10058 | - | - |
| 59 | 10059 | - | - |
| 60 | 10060 | - | - |
| 61 | 10061 | - | - |
| 62 | 10062 | - | - |
| 63 | 10063 | - | - |
| 64 | 10064 | - | - |
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
| customer7 | 7 | - |
| customer8 | 8 | - |
| customer9 | 9 | - |
| customer10 | 10 | - |
| customer11 | 11 | - |
| customer12 | 12 | - |
| customer13 | 13 | - |
| customer14 | 14 | - |
| customer15 | 15 | - |
| customer16 | 16 | - |
| customer17 | 17 | - |
| customer18 | 18 | - |
| customer19 | 19 | - |
| customer20 | 20 | - |
| customer21 | 21 | - |
| customer22 | 22 | - |
| customer23 | 23 | - |
| customer24 | 24 | - |
| customer25 | 25 | - |
| customer26 | 26 | - |
| customer27 | 27 | - |
| customer28 | 28 | - |
| customer29 | 29 | - |
| customer30 | 30 | - |
| customer31 | 31 | - |
| customer32 | 32 | - |
| customer33 | 33 | - |
| customer34 | 34 | - |
| customer35 | 35 | - |
| customer36 | 36 | - |
| customer37 | 37 | - |
| customer38 | 38 | - |
| customer39 | 39 | - |
| customer40 | 40 | - |
| customer41 | 41 | - |
| customer42 | 42 | - |
| customer43 | 43 | - |
| customer44 | 44 | - |
| customer45 | 45 | - |
| customer46 | 46 | - |
| customer47 | 47 | - |
| customer48 | 48 | - |
| customer49 | 49 | - |
| customer50 | 50 | - |
| customer51 | 51 | - |
| customer52 | 52 | - |
| customer53 | 53 | - |
| customer54 | 54 | - |
| customer55 | 55 | - |
| customer56 | 56 | - |
| customer57 | 57 | - |
| customer58 | 58 | - |
| customer59 | 59 | - |
| customer60 | 60 | - |
| customer61 | 61 | - |
| customer62 | 62 | - |
| customer63 | 63 | - |
| customer64 | 64 | - |

#### VXLAN Interface Device Configuration

```eos
!
interface Vxlan1
   description r1-site1-leaf1_VTEP
   vxlan source-interface Loopback1
   vxlan udp-port 4789
   vxlan vlan 2 vni 10002
   vxlan vlan 3 vni 10003
   vxlan vlan 4 vni 10004
   vxlan vlan 5 vni 10005
   vxlan vlan 6 vni 10006
   vxlan vlan 7 vni 10007
   vxlan vlan 8 vni 10008
   vxlan vlan 9 vni 10009
   vxlan vlan 10 vni 10010
   vxlan vlan 11 vni 10011
   vxlan vlan 12 vni 10012
   vxlan vlan 13 vni 10013
   vxlan vlan 14 vni 10014
   vxlan vlan 15 vni 10015
   vxlan vlan 16 vni 10016
   vxlan vlan 17 vni 10017
   vxlan vlan 18 vni 10018
   vxlan vlan 19 vni 10019
   vxlan vlan 20 vni 10020
   vxlan vlan 21 vni 10021
   vxlan vlan 22 vni 10022
   vxlan vlan 23 vni 10023
   vxlan vlan 24 vni 10024
   vxlan vlan 25 vni 10025
   vxlan vlan 26 vni 10026
   vxlan vlan 27 vni 10027
   vxlan vlan 28 vni 10028
   vxlan vlan 29 vni 10029
   vxlan vlan 30 vni 10030
   vxlan vlan 31 vni 10031
   vxlan vlan 32 vni 10032
   vxlan vlan 33 vni 10033
   vxlan vlan 34 vni 10034
   vxlan vlan 35 vni 10035
   vxlan vlan 36 vni 10036
   vxlan vlan 37 vni 10037
   vxlan vlan 38 vni 10038
   vxlan vlan 39 vni 10039
   vxlan vlan 40 vni 10040
   vxlan vlan 41 vni 10041
   vxlan vlan 42 vni 10042
   vxlan vlan 43 vni 10043
   vxlan vlan 44 vni 10044
   vxlan vlan 45 vni 10045
   vxlan vlan 46 vni 10046
   vxlan vlan 47 vni 10047
   vxlan vlan 48 vni 10048
   vxlan vlan 49 vni 10049
   vxlan vlan 50 vni 10050
   vxlan vlan 51 vni 10051
   vxlan vlan 52 vni 10052
   vxlan vlan 53 vni 10053
   vxlan vlan 54 vni 10054
   vxlan vlan 55 vni 10055
   vxlan vlan 56 vni 10056
   vxlan vlan 57 vni 10057
   vxlan vlan 58 vni 10058
   vxlan vlan 59 vni 10059
   vxlan vlan 60 vni 10060
   vxlan vlan 61 vni 10061
   vxlan vlan 62 vni 10062
   vxlan vlan 63 vni 10063
   vxlan vlan 64 vni 10064
   vxlan vlan 254 vni 10254
   vxlan vrf customer1 vni 254
   vxlan vrf customer2 vni 2
   vxlan vrf customer3 vni 3
   vxlan vrf customer4 vni 4
   vxlan vrf customer5 vni 5
   vxlan vrf customer6 vni 6
   vxlan vrf customer7 vni 7
   vxlan vrf customer8 vni 8
   vxlan vrf customer9 vni 9
   vxlan vrf customer10 vni 10
   vxlan vrf customer11 vni 11
   vxlan vrf customer12 vni 12
   vxlan vrf customer13 vni 13
   vxlan vrf customer14 vni 14
   vxlan vrf customer15 vni 15
   vxlan vrf customer16 vni 16
   vxlan vrf customer17 vni 17
   vxlan vrf customer18 vni 18
   vxlan vrf customer19 vni 19
   vxlan vrf customer20 vni 20
   vxlan vrf customer21 vni 21
   vxlan vrf customer22 vni 22
   vxlan vrf customer23 vni 23
   vxlan vrf customer24 vni 24
   vxlan vrf customer25 vni 25
   vxlan vrf customer26 vni 26
   vxlan vrf customer27 vni 27
   vxlan vrf customer28 vni 28
   vxlan vrf customer29 vni 29
   vxlan vrf customer30 vni 30
   vxlan vrf customer31 vni 31
   vxlan vrf customer32 vni 32
   vxlan vrf customer33 vni 33
   vxlan vrf customer34 vni 34
   vxlan vrf customer35 vni 35
   vxlan vrf customer36 vni 36
   vxlan vrf customer37 vni 37
   vxlan vrf customer38 vni 38
   vxlan vrf customer39 vni 39
   vxlan vrf customer40 vni 40
   vxlan vrf customer41 vni 41
   vxlan vrf customer42 vni 42
   vxlan vrf customer43 vni 43
   vxlan vrf customer44 vni 44
   vxlan vrf customer45 vni 45
   vxlan vrf customer46 vni 46
   vxlan vrf customer47 vni 47
   vxlan vrf customer48 vni 48
   vxlan vrf customer49 vni 49
   vxlan vrf customer50 vni 50
   vxlan vrf customer51 vni 51
   vxlan vrf customer52 vni 52
   vxlan vrf customer53 vni 53
   vxlan vrf customer54 vni 54
   vxlan vrf customer55 vni 55
   vxlan vrf customer56 vni 56
   vxlan vrf customer57 vni 57
   vxlan vrf customer58 vni 58
   vxlan vrf customer59 vni 59
   vxlan vrf customer60 vni 60
   vxlan vrf customer61 vni 61
   vxlan vrf customer62 vni 62
   vxlan vrf customer63 vni 63
   vxlan vrf customer64 vni 64
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
| customer7 | True |
| customer8 | True |
| customer9 | True |
| customer10 | True |
| customer11 | True |
| customer12 | True |
| customer13 | True |
| customer14 | True |
| customer15 | True |
| customer16 | True |
| customer17 | True |
| customer18 | True |
| customer19 | True |
| customer20 | True |
| customer21 | True |
| customer22 | True |
| customer23 | True |
| customer24 | True |
| customer25 | True |
| customer26 | True |
| customer27 | True |
| customer28 | True |
| customer29 | True |
| customer30 | True |
| customer31 | True |
| customer32 | True |
| customer33 | True |
| customer34 | True |
| customer35 | True |
| customer36 | True |
| customer37 | True |
| customer38 | True |
| customer39 | True |
| customer40 | True |
| customer41 | True |
| customer42 | True |
| customer43 | True |
| customer44 | True |
| customer45 | True |
| customer46 | True |
| customer47 | True |
| customer48 | True |
| customer49 | True |
| customer50 | True |
| customer51 | True |
| customer52 | True |
| customer53 | True |
| customer54 | True |
| customer55 | True |
| customer56 | True |
| customer57 | True |
| customer58 | True |
| customer59 | True |
| customer60 | True |
| customer61 | True |
| customer62 | True |
| customer63 | True |
| customer64 | True |
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
ip routing vrf customer7
ip routing vrf customer8
ip routing vrf customer9
ip routing vrf customer10
ip routing vrf customer11
ip routing vrf customer12
ip routing vrf customer13
ip routing vrf customer14
ip routing vrf customer15
ip routing vrf customer16
ip routing vrf customer17
ip routing vrf customer18
ip routing vrf customer19
ip routing vrf customer20
ip routing vrf customer21
ip routing vrf customer22
ip routing vrf customer23
ip routing vrf customer24
ip routing vrf customer25
ip routing vrf customer26
ip routing vrf customer27
ip routing vrf customer28
ip routing vrf customer29
ip routing vrf customer30
ip routing vrf customer31
ip routing vrf customer32
ip routing vrf customer33
ip routing vrf customer34
ip routing vrf customer35
ip routing vrf customer36
ip routing vrf customer37
ip routing vrf customer38
ip routing vrf customer39
ip routing vrf customer40
ip routing vrf customer41
ip routing vrf customer42
ip routing vrf customer43
ip routing vrf customer44
ip routing vrf customer45
ip routing vrf customer46
ip routing vrf customer47
ip routing vrf customer48
ip routing vrf customer49
ip routing vrf customer50
ip routing vrf customer51
ip routing vrf customer52
ip routing vrf customer53
ip routing vrf customer54
ip routing vrf customer55
ip routing vrf customer56
ip routing vrf customer57
ip routing vrf customer58
ip routing vrf customer59
ip routing vrf customer60
ip routing vrf customer61
ip routing vrf customer62
ip routing vrf customer63
ip routing vrf customer64
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
| customer7 | false |
| customer8 | false |
| customer9 | false |
| customer10 | false |
| customer11 | false |
| customer12 | false |
| customer13 | false |
| customer14 | false |
| customer15 | false |
| customer16 | false |
| customer17 | false |
| customer18 | false |
| customer19 | false |
| customer20 | false |
| customer21 | false |
| customer22 | false |
| customer23 | false |
| customer24 | false |
| customer25 | false |
| customer26 | false |
| customer27 | false |
| customer28 | false |
| customer29 | false |
| customer30 | false |
| customer31 | false |
| customer32 | false |
| customer33 | false |
| customer34 | false |
| customer35 | false |
| customer36 | false |
| customer37 | false |
| customer38 | false |
| customer39 | false |
| customer40 | false |
| customer41 | false |
| customer42 | false |
| customer43 | false |
| customer44 | false |
| customer45 | false |
| customer46 | false |
| customer47 | false |
| customer48 | false |
| customer49 | false |
| customer50 | false |
| customer51 | false |
| customer52 | false |
| customer53 | false |
| customer54 | false |
| customer55 | false |
| customer56 | false |
| customer57 | false |
| customer58 | false |
| customer59 | false |
| customer60 | false |
| customer61 | false |
| customer62 | false |
| customer63 | false |
| customer64 | false |
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
| 65011 | 10.1.1.3 |

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
| 10.1.1.129 | 65000 | default | - | Inherited from peer group IPv4-UNDERLAY-PEERS | Inherited from peer group IPv4-UNDERLAY-PEERS | - | - | - | - | - | - |
| 10.1.1.131 | 65000 | default | - | Inherited from peer group IPv4-UNDERLAY-PEERS | Inherited from peer group IPv4-UNDERLAY-PEERS | - | - | - | - | - | - |
| 10.1.1.129 | 65000 | customer1 | - | Inherited from peer group IPv4-UNDERLAY-PEERS | Inherited from peer group IPv4-UNDERLAY-PEERS | - | - | - | - | - | - |
| 10.1.1.131 | 65000 | customer1 | - | Inherited from peer group IPv4-UNDERLAY-PEERS | Inherited from peer group IPv4-UNDERLAY-PEERS | - | - | - | - | - | - |
| 10.1.1.129 | 65000 | customer2 | - | Inherited from peer group IPv4-UNDERLAY-PEERS | Inherited from peer group IPv4-UNDERLAY-PEERS | - | - | - | - | - | - |
| 10.1.1.131 | 65000 | customer2 | - | Inherited from peer group IPv4-UNDERLAY-PEERS | Inherited from peer group IPv4-UNDERLAY-PEERS | - | - | - | - | - | - |
| 10.1.1.129 | 65000 | customer3 | - | Inherited from peer group IPv4-UNDERLAY-PEERS | Inherited from peer group IPv4-UNDERLAY-PEERS | - | - | - | - | - | - |
| 10.1.1.131 | 65000 | customer3 | - | Inherited from peer group IPv4-UNDERLAY-PEERS | Inherited from peer group IPv4-UNDERLAY-PEERS | - | - | - | - | - | - |
| 10.1.1.129 | 65000 | customer4 | - | Inherited from peer group IPv4-UNDERLAY-PEERS | Inherited from peer group IPv4-UNDERLAY-PEERS | - | - | - | - | - | - |
| 10.1.1.131 | 65000 | customer4 | - | Inherited from peer group IPv4-UNDERLAY-PEERS | Inherited from peer group IPv4-UNDERLAY-PEERS | - | - | - | - | - | - |
| 10.1.1.129 | 65000 | customer5 | - | Inherited from peer group IPv4-UNDERLAY-PEERS | Inherited from peer group IPv4-UNDERLAY-PEERS | - | - | - | - | - | - |
| 10.1.1.131 | 65000 | customer5 | - | Inherited from peer group IPv4-UNDERLAY-PEERS | Inherited from peer group IPv4-UNDERLAY-PEERS | - | - | - | - | - | - |
| 10.1.1.129 | 65000 | customer6 | - | Inherited from peer group IPv4-UNDERLAY-PEERS | Inherited from peer group IPv4-UNDERLAY-PEERS | - | - | - | - | - | - |
| 10.1.1.131 | 65000 | customer6 | - | Inherited from peer group IPv4-UNDERLAY-PEERS | Inherited from peer group IPv4-UNDERLAY-PEERS | - | - | - | - | - | - |
| 10.1.1.129 | 65000 | customer7 | - | Inherited from peer group IPv4-UNDERLAY-PEERS | Inherited from peer group IPv4-UNDERLAY-PEERS | - | - | - | - | - | - |
| 10.1.1.131 | 65000 | customer7 | - | Inherited from peer group IPv4-UNDERLAY-PEERS | Inherited from peer group IPv4-UNDERLAY-PEERS | - | - | - | - | - | - |
| 10.1.1.129 | 65000 | customer8 | - | Inherited from peer group IPv4-UNDERLAY-PEERS | Inherited from peer group IPv4-UNDERLAY-PEERS | - | - | - | - | - | - |
| 10.1.1.131 | 65000 | customer8 | - | Inherited from peer group IPv4-UNDERLAY-PEERS | Inherited from peer group IPv4-UNDERLAY-PEERS | - | - | - | - | - | - |
| 10.1.1.129 | 65000 | customer9 | - | Inherited from peer group IPv4-UNDERLAY-PEERS | Inherited from peer group IPv4-UNDERLAY-PEERS | - | - | - | - | - | - |
| 10.1.1.131 | 65000 | customer9 | - | Inherited from peer group IPv4-UNDERLAY-PEERS | Inherited from peer group IPv4-UNDERLAY-PEERS | - | - | - | - | - | - |
| 10.1.1.129 | 65000 | customer10 | - | Inherited from peer group IPv4-UNDERLAY-PEERS | Inherited from peer group IPv4-UNDERLAY-PEERS | - | - | - | - | - | - |
| 10.1.1.131 | 65000 | customer10 | - | Inherited from peer group IPv4-UNDERLAY-PEERS | Inherited from peer group IPv4-UNDERLAY-PEERS | - | - | - | - | - | - |
| 10.1.1.129 | 65000 | customer11 | - | Inherited from peer group IPv4-UNDERLAY-PEERS | Inherited from peer group IPv4-UNDERLAY-PEERS | - | - | - | - | - | - |
| 10.1.1.131 | 65000 | customer11 | - | Inherited from peer group IPv4-UNDERLAY-PEERS | Inherited from peer group IPv4-UNDERLAY-PEERS | - | - | - | - | - | - |
| 10.1.1.129 | 65000 | customer12 | - | Inherited from peer group IPv4-UNDERLAY-PEERS | Inherited from peer group IPv4-UNDERLAY-PEERS | - | - | - | - | - | - |
| 10.1.1.131 | 65000 | customer12 | - | Inherited from peer group IPv4-UNDERLAY-PEERS | Inherited from peer group IPv4-UNDERLAY-PEERS | - | - | - | - | - | - |
| 10.1.1.129 | 65000 | customer13 | - | Inherited from peer group IPv4-UNDERLAY-PEERS | Inherited from peer group IPv4-UNDERLAY-PEERS | - | - | - | - | - | - |
| 10.1.1.131 | 65000 | customer13 | - | Inherited from peer group IPv4-UNDERLAY-PEERS | Inherited from peer group IPv4-UNDERLAY-PEERS | - | - | - | - | - | - |
| 10.1.1.129 | 65000 | customer14 | - | Inherited from peer group IPv4-UNDERLAY-PEERS | Inherited from peer group IPv4-UNDERLAY-PEERS | - | - | - | - | - | - |
| 10.1.1.131 | 65000 | customer14 | - | Inherited from peer group IPv4-UNDERLAY-PEERS | Inherited from peer group IPv4-UNDERLAY-PEERS | - | - | - | - | - | - |
| 10.1.1.129 | 65000 | customer15 | - | Inherited from peer group IPv4-UNDERLAY-PEERS | Inherited from peer group IPv4-UNDERLAY-PEERS | - | - | - | - | - | - |
| 10.1.1.131 | 65000 | customer15 | - | Inherited from peer group IPv4-UNDERLAY-PEERS | Inherited from peer group IPv4-UNDERLAY-PEERS | - | - | - | - | - | - |
| 10.1.1.129 | 65000 | customer16 | - | Inherited from peer group IPv4-UNDERLAY-PEERS | Inherited from peer group IPv4-UNDERLAY-PEERS | - | - | - | - | - | - |
| 10.1.1.131 | 65000 | customer16 | - | Inherited from peer group IPv4-UNDERLAY-PEERS | Inherited from peer group IPv4-UNDERLAY-PEERS | - | - | - | - | - | - |
| 10.1.1.129 | 65000 | customer17 | - | Inherited from peer group IPv4-UNDERLAY-PEERS | Inherited from peer group IPv4-UNDERLAY-PEERS | - | - | - | - | - | - |
| 10.1.1.131 | 65000 | customer17 | - | Inherited from peer group IPv4-UNDERLAY-PEERS | Inherited from peer group IPv4-UNDERLAY-PEERS | - | - | - | - | - | - |
| 10.1.1.129 | 65000 | customer18 | - | Inherited from peer group IPv4-UNDERLAY-PEERS | Inherited from peer group IPv4-UNDERLAY-PEERS | - | - | - | - | - | - |
| 10.1.1.131 | 65000 | customer18 | - | Inherited from peer group IPv4-UNDERLAY-PEERS | Inherited from peer group IPv4-UNDERLAY-PEERS | - | - | - | - | - | - |
| 10.1.1.129 | 65000 | customer19 | - | Inherited from peer group IPv4-UNDERLAY-PEERS | Inherited from peer group IPv4-UNDERLAY-PEERS | - | - | - | - | - | - |
| 10.1.1.131 | 65000 | customer19 | - | Inherited from peer group IPv4-UNDERLAY-PEERS | Inherited from peer group IPv4-UNDERLAY-PEERS | - | - | - | - | - | - |
| 10.1.1.129 | 65000 | customer20 | - | Inherited from peer group IPv4-UNDERLAY-PEERS | Inherited from peer group IPv4-UNDERLAY-PEERS | - | - | - | - | - | - |
| 10.1.1.131 | 65000 | customer20 | - | Inherited from peer group IPv4-UNDERLAY-PEERS | Inherited from peer group IPv4-UNDERLAY-PEERS | - | - | - | - | - | - |
| 10.1.1.129 | 65000 | customer21 | - | Inherited from peer group IPv4-UNDERLAY-PEERS | Inherited from peer group IPv4-UNDERLAY-PEERS | - | - | - | - | - | - |
| 10.1.1.131 | 65000 | customer21 | - | Inherited from peer group IPv4-UNDERLAY-PEERS | Inherited from peer group IPv4-UNDERLAY-PEERS | - | - | - | - | - | - |
| 10.1.1.129 | 65000 | customer22 | - | Inherited from peer group IPv4-UNDERLAY-PEERS | Inherited from peer group IPv4-UNDERLAY-PEERS | - | - | - | - | - | - |
| 10.1.1.131 | 65000 | customer22 | - | Inherited from peer group IPv4-UNDERLAY-PEERS | Inherited from peer group IPv4-UNDERLAY-PEERS | - | - | - | - | - | - |
| 10.1.1.129 | 65000 | customer23 | - | Inherited from peer group IPv4-UNDERLAY-PEERS | Inherited from peer group IPv4-UNDERLAY-PEERS | - | - | - | - | - | - |
| 10.1.1.131 | 65000 | customer23 | - | Inherited from peer group IPv4-UNDERLAY-PEERS | Inherited from peer group IPv4-UNDERLAY-PEERS | - | - | - | - | - | - |
| 10.1.1.129 | 65000 | customer24 | - | Inherited from peer group IPv4-UNDERLAY-PEERS | Inherited from peer group IPv4-UNDERLAY-PEERS | - | - | - | - | - | - |
| 10.1.1.131 | 65000 | customer24 | - | Inherited from peer group IPv4-UNDERLAY-PEERS | Inherited from peer group IPv4-UNDERLAY-PEERS | - | - | - | - | - | - |
| 10.1.1.129 | 65000 | customer25 | - | Inherited from peer group IPv4-UNDERLAY-PEERS | Inherited from peer group IPv4-UNDERLAY-PEERS | - | - | - | - | - | - |
| 10.1.1.131 | 65000 | customer25 | - | Inherited from peer group IPv4-UNDERLAY-PEERS | Inherited from peer group IPv4-UNDERLAY-PEERS | - | - | - | - | - | - |
| 10.1.1.129 | 65000 | customer26 | - | Inherited from peer group IPv4-UNDERLAY-PEERS | Inherited from peer group IPv4-UNDERLAY-PEERS | - | - | - | - | - | - |
| 10.1.1.131 | 65000 | customer26 | - | Inherited from peer group IPv4-UNDERLAY-PEERS | Inherited from peer group IPv4-UNDERLAY-PEERS | - | - | - | - | - | - |
| 10.1.1.129 | 65000 | customer27 | - | Inherited from peer group IPv4-UNDERLAY-PEERS | Inherited from peer group IPv4-UNDERLAY-PEERS | - | - | - | - | - | - |
| 10.1.1.131 | 65000 | customer27 | - | Inherited from peer group IPv4-UNDERLAY-PEERS | Inherited from peer group IPv4-UNDERLAY-PEERS | - | - | - | - | - | - |
| 10.1.1.129 | 65000 | customer28 | - | Inherited from peer group IPv4-UNDERLAY-PEERS | Inherited from peer group IPv4-UNDERLAY-PEERS | - | - | - | - | - | - |
| 10.1.1.131 | 65000 | customer28 | - | Inherited from peer group IPv4-UNDERLAY-PEERS | Inherited from peer group IPv4-UNDERLAY-PEERS | - | - | - | - | - | - |
| 10.1.1.129 | 65000 | customer29 | - | Inherited from peer group IPv4-UNDERLAY-PEERS | Inherited from peer group IPv4-UNDERLAY-PEERS | - | - | - | - | - | - |
| 10.1.1.131 | 65000 | customer29 | - | Inherited from peer group IPv4-UNDERLAY-PEERS | Inherited from peer group IPv4-UNDERLAY-PEERS | - | - | - | - | - | - |
| 10.1.1.129 | 65000 | customer30 | - | Inherited from peer group IPv4-UNDERLAY-PEERS | Inherited from peer group IPv4-UNDERLAY-PEERS | - | - | - | - | - | - |
| 10.1.1.131 | 65000 | customer30 | - | Inherited from peer group IPv4-UNDERLAY-PEERS | Inherited from peer group IPv4-UNDERLAY-PEERS | - | - | - | - | - | - |
| 10.1.1.129 | 65000 | customer31 | - | Inherited from peer group IPv4-UNDERLAY-PEERS | Inherited from peer group IPv4-UNDERLAY-PEERS | - | - | - | - | - | - |
| 10.1.1.131 | 65000 | customer31 | - | Inherited from peer group IPv4-UNDERLAY-PEERS | Inherited from peer group IPv4-UNDERLAY-PEERS | - | - | - | - | - | - |
| 10.1.1.129 | 65000 | customer32 | - | Inherited from peer group IPv4-UNDERLAY-PEERS | Inherited from peer group IPv4-UNDERLAY-PEERS | - | - | - | - | - | - |
| 10.1.1.131 | 65000 | customer32 | - | Inherited from peer group IPv4-UNDERLAY-PEERS | Inherited from peer group IPv4-UNDERLAY-PEERS | - | - | - | - | - | - |
| 10.1.1.129 | 65000 | customer33 | - | Inherited from peer group IPv4-UNDERLAY-PEERS | Inherited from peer group IPv4-UNDERLAY-PEERS | - | - | - | - | - | - |
| 10.1.1.131 | 65000 | customer33 | - | Inherited from peer group IPv4-UNDERLAY-PEERS | Inherited from peer group IPv4-UNDERLAY-PEERS | - | - | - | - | - | - |
| 10.1.1.129 | 65000 | customer34 | - | Inherited from peer group IPv4-UNDERLAY-PEERS | Inherited from peer group IPv4-UNDERLAY-PEERS | - | - | - | - | - | - |
| 10.1.1.131 | 65000 | customer34 | - | Inherited from peer group IPv4-UNDERLAY-PEERS | Inherited from peer group IPv4-UNDERLAY-PEERS | - | - | - | - | - | - |
| 10.1.1.129 | 65000 | customer35 | - | Inherited from peer group IPv4-UNDERLAY-PEERS | Inherited from peer group IPv4-UNDERLAY-PEERS | - | - | - | - | - | - |
| 10.1.1.131 | 65000 | customer35 | - | Inherited from peer group IPv4-UNDERLAY-PEERS | Inherited from peer group IPv4-UNDERLAY-PEERS | - | - | - | - | - | - |
| 10.1.1.129 | 65000 | customer36 | - | Inherited from peer group IPv4-UNDERLAY-PEERS | Inherited from peer group IPv4-UNDERLAY-PEERS | - | - | - | - | - | - |
| 10.1.1.131 | 65000 | customer36 | - | Inherited from peer group IPv4-UNDERLAY-PEERS | Inherited from peer group IPv4-UNDERLAY-PEERS | - | - | - | - | - | - |
| 10.1.1.129 | 65000 | customer37 | - | Inherited from peer group IPv4-UNDERLAY-PEERS | Inherited from peer group IPv4-UNDERLAY-PEERS | - | - | - | - | - | - |
| 10.1.1.131 | 65000 | customer37 | - | Inherited from peer group IPv4-UNDERLAY-PEERS | Inherited from peer group IPv4-UNDERLAY-PEERS | - | - | - | - | - | - |
| 10.1.1.129 | 65000 | customer38 | - | Inherited from peer group IPv4-UNDERLAY-PEERS | Inherited from peer group IPv4-UNDERLAY-PEERS | - | - | - | - | - | - |
| 10.1.1.131 | 65000 | customer38 | - | Inherited from peer group IPv4-UNDERLAY-PEERS | Inherited from peer group IPv4-UNDERLAY-PEERS | - | - | - | - | - | - |
| 10.1.1.129 | 65000 | customer39 | - | Inherited from peer group IPv4-UNDERLAY-PEERS | Inherited from peer group IPv4-UNDERLAY-PEERS | - | - | - | - | - | - |
| 10.1.1.131 | 65000 | customer39 | - | Inherited from peer group IPv4-UNDERLAY-PEERS | Inherited from peer group IPv4-UNDERLAY-PEERS | - | - | - | - | - | - |
| 10.1.1.129 | 65000 | customer40 | - | Inherited from peer group IPv4-UNDERLAY-PEERS | Inherited from peer group IPv4-UNDERLAY-PEERS | - | - | - | - | - | - |
| 10.1.1.131 | 65000 | customer40 | - | Inherited from peer group IPv4-UNDERLAY-PEERS | Inherited from peer group IPv4-UNDERLAY-PEERS | - | - | - | - | - | - |
| 10.1.1.129 | 65000 | customer41 | - | Inherited from peer group IPv4-UNDERLAY-PEERS | Inherited from peer group IPv4-UNDERLAY-PEERS | - | - | - | - | - | - |
| 10.1.1.131 | 65000 | customer41 | - | Inherited from peer group IPv4-UNDERLAY-PEERS | Inherited from peer group IPv4-UNDERLAY-PEERS | - | - | - | - | - | - |
| 10.1.1.129 | 65000 | customer42 | - | Inherited from peer group IPv4-UNDERLAY-PEERS | Inherited from peer group IPv4-UNDERLAY-PEERS | - | - | - | - | - | - |
| 10.1.1.131 | 65000 | customer42 | - | Inherited from peer group IPv4-UNDERLAY-PEERS | Inherited from peer group IPv4-UNDERLAY-PEERS | - | - | - | - | - | - |
| 10.1.1.129 | 65000 | customer43 | - | Inherited from peer group IPv4-UNDERLAY-PEERS | Inherited from peer group IPv4-UNDERLAY-PEERS | - | - | - | - | - | - |
| 10.1.1.131 | 65000 | customer43 | - | Inherited from peer group IPv4-UNDERLAY-PEERS | Inherited from peer group IPv4-UNDERLAY-PEERS | - | - | - | - | - | - |
| 10.1.1.129 | 65000 | customer44 | - | Inherited from peer group IPv4-UNDERLAY-PEERS | Inherited from peer group IPv4-UNDERLAY-PEERS | - | - | - | - | - | - |
| 10.1.1.131 | 65000 | customer44 | - | Inherited from peer group IPv4-UNDERLAY-PEERS | Inherited from peer group IPv4-UNDERLAY-PEERS | - | - | - | - | - | - |
| 10.1.1.129 | 65000 | customer45 | - | Inherited from peer group IPv4-UNDERLAY-PEERS | Inherited from peer group IPv4-UNDERLAY-PEERS | - | - | - | - | - | - |
| 10.1.1.131 | 65000 | customer45 | - | Inherited from peer group IPv4-UNDERLAY-PEERS | Inherited from peer group IPv4-UNDERLAY-PEERS | - | - | - | - | - | - |
| 10.1.1.129 | 65000 | customer46 | - | Inherited from peer group IPv4-UNDERLAY-PEERS | Inherited from peer group IPv4-UNDERLAY-PEERS | - | - | - | - | - | - |
| 10.1.1.131 | 65000 | customer46 | - | Inherited from peer group IPv4-UNDERLAY-PEERS | Inherited from peer group IPv4-UNDERLAY-PEERS | - | - | - | - | - | - |
| 10.1.1.129 | 65000 | customer47 | - | Inherited from peer group IPv4-UNDERLAY-PEERS | Inherited from peer group IPv4-UNDERLAY-PEERS | - | - | - | - | - | - |
| 10.1.1.131 | 65000 | customer47 | - | Inherited from peer group IPv4-UNDERLAY-PEERS | Inherited from peer group IPv4-UNDERLAY-PEERS | - | - | - | - | - | - |
| 10.1.1.129 | 65000 | customer48 | - | Inherited from peer group IPv4-UNDERLAY-PEERS | Inherited from peer group IPv4-UNDERLAY-PEERS | - | - | - | - | - | - |
| 10.1.1.131 | 65000 | customer48 | - | Inherited from peer group IPv4-UNDERLAY-PEERS | Inherited from peer group IPv4-UNDERLAY-PEERS | - | - | - | - | - | - |
| 10.1.1.129 | 65000 | customer49 | - | Inherited from peer group IPv4-UNDERLAY-PEERS | Inherited from peer group IPv4-UNDERLAY-PEERS | - | - | - | - | - | - |
| 10.1.1.131 | 65000 | customer49 | - | Inherited from peer group IPv4-UNDERLAY-PEERS | Inherited from peer group IPv4-UNDERLAY-PEERS | - | - | - | - | - | - |
| 10.1.1.129 | 65000 | customer50 | - | Inherited from peer group IPv4-UNDERLAY-PEERS | Inherited from peer group IPv4-UNDERLAY-PEERS | - | - | - | - | - | - |
| 10.1.1.131 | 65000 | customer50 | - | Inherited from peer group IPv4-UNDERLAY-PEERS | Inherited from peer group IPv4-UNDERLAY-PEERS | - | - | - | - | - | - |
| 10.1.1.129 | 65000 | customer51 | - | Inherited from peer group IPv4-UNDERLAY-PEERS | Inherited from peer group IPv4-UNDERLAY-PEERS | - | - | - | - | - | - |
| 10.1.1.131 | 65000 | customer51 | - | Inherited from peer group IPv4-UNDERLAY-PEERS | Inherited from peer group IPv4-UNDERLAY-PEERS | - | - | - | - | - | - |
| 10.1.1.129 | 65000 | customer52 | - | Inherited from peer group IPv4-UNDERLAY-PEERS | Inherited from peer group IPv4-UNDERLAY-PEERS | - | - | - | - | - | - |
| 10.1.1.131 | 65000 | customer52 | - | Inherited from peer group IPv4-UNDERLAY-PEERS | Inherited from peer group IPv4-UNDERLAY-PEERS | - | - | - | - | - | - |
| 10.1.1.129 | 65000 | customer53 | - | Inherited from peer group IPv4-UNDERLAY-PEERS | Inherited from peer group IPv4-UNDERLAY-PEERS | - | - | - | - | - | - |
| 10.1.1.131 | 65000 | customer53 | - | Inherited from peer group IPv4-UNDERLAY-PEERS | Inherited from peer group IPv4-UNDERLAY-PEERS | - | - | - | - | - | - |
| 10.1.1.129 | 65000 | customer54 | - | Inherited from peer group IPv4-UNDERLAY-PEERS | Inherited from peer group IPv4-UNDERLAY-PEERS | - | - | - | - | - | - |
| 10.1.1.131 | 65000 | customer54 | - | Inherited from peer group IPv4-UNDERLAY-PEERS | Inherited from peer group IPv4-UNDERLAY-PEERS | - | - | - | - | - | - |
| 10.1.1.129 | 65000 | customer55 | - | Inherited from peer group IPv4-UNDERLAY-PEERS | Inherited from peer group IPv4-UNDERLAY-PEERS | - | - | - | - | - | - |
| 10.1.1.131 | 65000 | customer55 | - | Inherited from peer group IPv4-UNDERLAY-PEERS | Inherited from peer group IPv4-UNDERLAY-PEERS | - | - | - | - | - | - |
| 10.1.1.129 | 65000 | customer56 | - | Inherited from peer group IPv4-UNDERLAY-PEERS | Inherited from peer group IPv4-UNDERLAY-PEERS | - | - | - | - | - | - |
| 10.1.1.131 | 65000 | customer56 | - | Inherited from peer group IPv4-UNDERLAY-PEERS | Inherited from peer group IPv4-UNDERLAY-PEERS | - | - | - | - | - | - |
| 10.1.1.129 | 65000 | customer57 | - | Inherited from peer group IPv4-UNDERLAY-PEERS | Inherited from peer group IPv4-UNDERLAY-PEERS | - | - | - | - | - | - |
| 10.1.1.131 | 65000 | customer57 | - | Inherited from peer group IPv4-UNDERLAY-PEERS | Inherited from peer group IPv4-UNDERLAY-PEERS | - | - | - | - | - | - |
| 10.1.1.129 | 65000 | customer58 | - | Inherited from peer group IPv4-UNDERLAY-PEERS | Inherited from peer group IPv4-UNDERLAY-PEERS | - | - | - | - | - | - |
| 10.1.1.131 | 65000 | customer58 | - | Inherited from peer group IPv4-UNDERLAY-PEERS | Inherited from peer group IPv4-UNDERLAY-PEERS | - | - | - | - | - | - |
| 10.1.1.129 | 65000 | customer59 | - | Inherited from peer group IPv4-UNDERLAY-PEERS | Inherited from peer group IPv4-UNDERLAY-PEERS | - | - | - | - | - | - |
| 10.1.1.131 | 65000 | customer59 | - | Inherited from peer group IPv4-UNDERLAY-PEERS | Inherited from peer group IPv4-UNDERLAY-PEERS | - | - | - | - | - | - |
| 10.1.1.129 | 65000 | customer60 | - | Inherited from peer group IPv4-UNDERLAY-PEERS | Inherited from peer group IPv4-UNDERLAY-PEERS | - | - | - | - | - | - |
| 10.1.1.131 | 65000 | customer60 | - | Inherited from peer group IPv4-UNDERLAY-PEERS | Inherited from peer group IPv4-UNDERLAY-PEERS | - | - | - | - | - | - |
| 10.1.1.129 | 65000 | customer61 | - | Inherited from peer group IPv4-UNDERLAY-PEERS | Inherited from peer group IPv4-UNDERLAY-PEERS | - | - | - | - | - | - |
| 10.1.1.131 | 65000 | customer61 | - | Inherited from peer group IPv4-UNDERLAY-PEERS | Inherited from peer group IPv4-UNDERLAY-PEERS | - | - | - | - | - | - |
| 10.1.1.129 | 65000 | customer62 | - | Inherited from peer group IPv4-UNDERLAY-PEERS | Inherited from peer group IPv4-UNDERLAY-PEERS | - | - | - | - | - | - |
| 10.1.1.131 | 65000 | customer62 | - | Inherited from peer group IPv4-UNDERLAY-PEERS | Inherited from peer group IPv4-UNDERLAY-PEERS | - | - | - | - | - | - |
| 10.1.1.129 | 65000 | customer63 | - | Inherited from peer group IPv4-UNDERLAY-PEERS | Inherited from peer group IPv4-UNDERLAY-PEERS | - | - | - | - | - | - |
| 10.1.1.131 | 65000 | customer63 | - | Inherited from peer group IPv4-UNDERLAY-PEERS | Inherited from peer group IPv4-UNDERLAY-PEERS | - | - | - | - | - | - |
| 10.1.1.129 | 65000 | customer64 | - | Inherited from peer group IPv4-UNDERLAY-PEERS | Inherited from peer group IPv4-UNDERLAY-PEERS | - | - | - | - | - | - |
| 10.1.1.131 | 65000 | customer64 | - | Inherited from peer group IPv4-UNDERLAY-PEERS | Inherited from peer group IPv4-UNDERLAY-PEERS | - | - | - | - | - | - |

#### Router BGP EVPN Address Family

##### EVPN Peer Groups

| Peer Group | Activate | Encapsulation |
| ---------- | -------- | ------------- |
| EVPN-OVERLAY-PEERS | True | default |

#### Router BGP VLANs

| VLAN | Route-Distinguisher | Both Route-Target | Import Route Target | Export Route-Target | Redistribute |
| ---- | ------------------- | ----------------- | ------------------- | ------------------- | ------------ |
| 2 | 10.1.1.3:10002 | 10002:10002 | - | - | learned |
| 3 | 10.1.1.3:10003 | 10003:10003 | - | - | learned |
| 4 | 10.1.1.3:10004 | 10004:10004 | - | - | learned |
| 5 | 10.1.1.3:10005 | 10005:10005 | - | - | learned |
| 6 | 10.1.1.3:10006 | 10006:10006 | - | - | learned |
| 7 | 10.1.1.3:10007 | 10007:10007 | - | - | learned |
| 8 | 10.1.1.3:10008 | 10008:10008 | - | - | learned |
| 9 | 10.1.1.3:10009 | 10009:10009 | - | - | learned |
| 10 | 10.1.1.3:10010 | 10010:10010 | - | - | learned |
| 11 | 10.1.1.3:10011 | 10011:10011 | - | - | learned |
| 12 | 10.1.1.3:10012 | 10012:10012 | - | - | learned |
| 13 | 10.1.1.3:10013 | 10013:10013 | - | - | learned |
| 14 | 10.1.1.3:10014 | 10014:10014 | - | - | learned |
| 15 | 10.1.1.3:10015 | 10015:10015 | - | - | learned |
| 16 | 10.1.1.3:10016 | 10016:10016 | - | - | learned |
| 17 | 10.1.1.3:10017 | 10017:10017 | - | - | learned |
| 18 | 10.1.1.3:10018 | 10018:10018 | - | - | learned |
| 19 | 10.1.1.3:10019 | 10019:10019 | - | - | learned |
| 20 | 10.1.1.3:10020 | 10020:10020 | - | - | learned |
| 21 | 10.1.1.3:10021 | 10021:10021 | - | - | learned |
| 22 | 10.1.1.3:10022 | 10022:10022 | - | - | learned |
| 23 | 10.1.1.3:10023 | 10023:10023 | - | - | learned |
| 24 | 10.1.1.3:10024 | 10024:10024 | - | - | learned |
| 25 | 10.1.1.3:10025 | 10025:10025 | - | - | learned |
| 26 | 10.1.1.3:10026 | 10026:10026 | - | - | learned |
| 27 | 10.1.1.3:10027 | 10027:10027 | - | - | learned |
| 28 | 10.1.1.3:10028 | 10028:10028 | - | - | learned |
| 29 | 10.1.1.3:10029 | 10029:10029 | - | - | learned |
| 30 | 10.1.1.3:10030 | 10030:10030 | - | - | learned |
| 31 | 10.1.1.3:10031 | 10031:10031 | - | - | learned |
| 32 | 10.1.1.3:10032 | 10032:10032 | - | - | learned |
| 33 | 10.1.1.3:10033 | 10033:10033 | - | - | learned |
| 34 | 10.1.1.3:10034 | 10034:10034 | - | - | learned |
| 35 | 10.1.1.3:10035 | 10035:10035 | - | - | learned |
| 36 | 10.1.1.3:10036 | 10036:10036 | - | - | learned |
| 37 | 10.1.1.3:10037 | 10037:10037 | - | - | learned |
| 38 | 10.1.1.3:10038 | 10038:10038 | - | - | learned |
| 39 | 10.1.1.3:10039 | 10039:10039 | - | - | learned |
| 40 | 10.1.1.3:10040 | 10040:10040 | - | - | learned |
| 41 | 10.1.1.3:10041 | 10041:10041 | - | - | learned |
| 42 | 10.1.1.3:10042 | 10042:10042 | - | - | learned |
| 43 | 10.1.1.3:10043 | 10043:10043 | - | - | learned |
| 44 | 10.1.1.3:10044 | 10044:10044 | - | - | learned |
| 45 | 10.1.1.3:10045 | 10045:10045 | - | - | learned |
| 46 | 10.1.1.3:10046 | 10046:10046 | - | - | learned |
| 47 | 10.1.1.3:10047 | 10047:10047 | - | - | learned |
| 48 | 10.1.1.3:10048 | 10048:10048 | - | - | learned |
| 49 | 10.1.1.3:10049 | 10049:10049 | - | - | learned |
| 50 | 10.1.1.3:10050 | 10050:10050 | - | - | learned |
| 51 | 10.1.1.3:10051 | 10051:10051 | - | - | learned |
| 52 | 10.1.1.3:10052 | 10052:10052 | - | - | learned |
| 53 | 10.1.1.3:10053 | 10053:10053 | - | - | learned |
| 54 | 10.1.1.3:10054 | 10054:10054 | - | - | learned |
| 55 | 10.1.1.3:10055 | 10055:10055 | - | - | learned |
| 56 | 10.1.1.3:10056 | 10056:10056 | - | - | learned |
| 57 | 10.1.1.3:10057 | 10057:10057 | - | - | learned |
| 58 | 10.1.1.3:10058 | 10058:10058 | - | - | learned |
| 59 | 10.1.1.3:10059 | 10059:10059 | - | - | learned |
| 60 | 10.1.1.3:10060 | 10060:10060 | - | - | learned |
| 61 | 10.1.1.3:10061 | 10061:10061 | - | - | learned |
| 62 | 10.1.1.3:10062 | 10062:10062 | - | - | learned |
| 63 | 10.1.1.3:10063 | 10063:10063 | - | - | learned |
| 64 | 10.1.1.3:10064 | 10064:10064 | - | - | learned |
| 254 | 10.1.1.3:10254 | 10254:10254 | - | - | learned |

#### Router BGP VRFs

| VRF | Route-Distinguisher | Redistribute |
| --- | ------------------- | ------------ |
| customer1 | 10.1.1.3:254 | connected |
| customer2 | 10.1.1.3:2 | connected |
| customer3 | 10.1.1.3:3 | connected |
| customer4 | 10.1.1.3:4 | connected |
| customer5 | 10.1.1.3:5 | connected |
| customer6 | 10.1.1.3:6 | connected |
| customer7 | 10.1.1.3:7 | connected |
| customer8 | 10.1.1.3:8 | connected |
| customer9 | 10.1.1.3:9 | connected |
| customer10 | 10.1.1.3:10 | connected |
| customer11 | 10.1.1.3:11 | connected |
| customer12 | 10.1.1.3:12 | connected |
| customer13 | 10.1.1.3:13 | connected |
| customer14 | 10.1.1.3:14 | connected |
| customer15 | 10.1.1.3:15 | connected |
| customer16 | 10.1.1.3:16 | connected |
| customer17 | 10.1.1.3:17 | connected |
| customer18 | 10.1.1.3:18 | connected |
| customer19 | 10.1.1.3:19 | connected |
| customer20 | 10.1.1.3:20 | connected |
| customer21 | 10.1.1.3:21 | connected |
| customer22 | 10.1.1.3:22 | connected |
| customer23 | 10.1.1.3:23 | connected |
| customer24 | 10.1.1.3:24 | connected |
| customer25 | 10.1.1.3:25 | connected |
| customer26 | 10.1.1.3:26 | connected |
| customer27 | 10.1.1.3:27 | connected |
| customer28 | 10.1.1.3:28 | connected |
| customer29 | 10.1.1.3:29 | connected |
| customer30 | 10.1.1.3:30 | connected |
| customer31 | 10.1.1.3:31 | connected |
| customer32 | 10.1.1.3:32 | connected |
| customer33 | 10.1.1.3:33 | connected |
| customer34 | 10.1.1.3:34 | connected |
| customer35 | 10.1.1.3:35 | connected |
| customer36 | 10.1.1.3:36 | connected |
| customer37 | 10.1.1.3:37 | connected |
| customer38 | 10.1.1.3:38 | connected |
| customer39 | 10.1.1.3:39 | connected |
| customer40 | 10.1.1.3:40 | connected |
| customer41 | 10.1.1.3:41 | connected |
| customer42 | 10.1.1.3:42 | connected |
| customer43 | 10.1.1.3:43 | connected |
| customer44 | 10.1.1.3:44 | connected |
| customer45 | 10.1.1.3:45 | connected |
| customer46 | 10.1.1.3:46 | connected |
| customer47 | 10.1.1.3:47 | connected |
| customer48 | 10.1.1.3:48 | connected |
| customer49 | 10.1.1.3:49 | connected |
| customer50 | 10.1.1.3:50 | connected |
| customer51 | 10.1.1.3:51 | connected |
| customer52 | 10.1.1.3:52 | connected |
| customer53 | 10.1.1.3:53 | connected |
| customer54 | 10.1.1.3:54 | connected |
| customer55 | 10.1.1.3:55 | connected |
| customer56 | 10.1.1.3:56 | connected |
| customer57 | 10.1.1.3:57 | connected |
| customer58 | 10.1.1.3:58 | connected |
| customer59 | 10.1.1.3:59 | connected |
| customer60 | 10.1.1.3:60 | connected |
| customer61 | 10.1.1.3:61 | connected |
| customer62 | 10.1.1.3:62 | connected |
| customer63 | 10.1.1.3:63 | connected |
| customer64 | 10.1.1.3:64 | connected |

#### Router BGP Device Configuration

```eos
!
router bgp 65011
   router-id 10.1.1.3
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
   neighbor 10.1.1.129 peer group IPv4-UNDERLAY-PEERS
   neighbor 10.1.1.129 remote-as 65000
   neighbor 10.1.1.129 description r1-site1-wan1_Ethernet1/5
   neighbor 10.1.1.131 peer group IPv4-UNDERLAY-PEERS
   neighbor 10.1.1.131 remote-as 65000
   neighbor 10.1.1.131 description r1-site1-wan2_Ethernet1/5
   redistribute connected route-map RM-CONN-2-BGP
   !
   vlan 10
      rd 10.1.1.3:10010
      route-target both 10010:10010
      redistribute learned
   !
   vlan 11
      rd 10.1.1.3:10011
      route-target both 10011:10011
      redistribute learned
   !
   vlan 12
      rd 10.1.1.3:10012
      route-target both 10012:10012
      redistribute learned
   !
   vlan 13
      rd 10.1.1.3:10013
      route-target both 10013:10013
      redistribute learned
   !
   vlan 14
      rd 10.1.1.3:10014
      route-target both 10014:10014
      redistribute learned
   !
   vlan 15
      rd 10.1.1.3:10015
      route-target both 10015:10015
      redistribute learned
   !
   vlan 16
      rd 10.1.1.3:10016
      route-target both 10016:10016
      redistribute learned
   !
   vlan 17
      rd 10.1.1.3:10017
      route-target both 10017:10017
      redistribute learned
   !
   vlan 18
      rd 10.1.1.3:10018
      route-target both 10018:10018
      redistribute learned
   !
   vlan 19
      rd 10.1.1.3:10019
      route-target both 10019:10019
      redistribute learned
   !
   vlan 2
      rd 10.1.1.3:10002
      route-target both 10002:10002
      redistribute learned
   !
   vlan 20
      rd 10.1.1.3:10020
      route-target both 10020:10020
      redistribute learned
   !
   vlan 21
      rd 10.1.1.3:10021
      route-target both 10021:10021
      redistribute learned
   !
   vlan 22
      rd 10.1.1.3:10022
      route-target both 10022:10022
      redistribute learned
   !
   vlan 23
      rd 10.1.1.3:10023
      route-target both 10023:10023
      redistribute learned
   !
   vlan 24
      rd 10.1.1.3:10024
      route-target both 10024:10024
      redistribute learned
   !
   vlan 25
      rd 10.1.1.3:10025
      route-target both 10025:10025
      redistribute learned
   !
   vlan 254
      rd 10.1.1.3:10254
      route-target both 10254:10254
      redistribute learned
   !
   vlan 26
      rd 10.1.1.3:10026
      route-target both 10026:10026
      redistribute learned
   !
   vlan 27
      rd 10.1.1.3:10027
      route-target both 10027:10027
      redistribute learned
   !
   vlan 28
      rd 10.1.1.3:10028
      route-target both 10028:10028
      redistribute learned
   !
   vlan 29
      rd 10.1.1.3:10029
      route-target both 10029:10029
      redistribute learned
   !
   vlan 3
      rd 10.1.1.3:10003
      route-target both 10003:10003
      redistribute learned
   !
   vlan 30
      rd 10.1.1.3:10030
      route-target both 10030:10030
      redistribute learned
   !
   vlan 31
      rd 10.1.1.3:10031
      route-target both 10031:10031
      redistribute learned
   !
   vlan 32
      rd 10.1.1.3:10032
      route-target both 10032:10032
      redistribute learned
   !
   vlan 33
      rd 10.1.1.3:10033
      route-target both 10033:10033
      redistribute learned
   !
   vlan 34
      rd 10.1.1.3:10034
      route-target both 10034:10034
      redistribute learned
   !
   vlan 35
      rd 10.1.1.3:10035
      route-target both 10035:10035
      redistribute learned
   !
   vlan 36
      rd 10.1.1.3:10036
      route-target both 10036:10036
      redistribute learned
   !
   vlan 37
      rd 10.1.1.3:10037
      route-target both 10037:10037
      redistribute learned
   !
   vlan 38
      rd 10.1.1.3:10038
      route-target both 10038:10038
      redistribute learned
   !
   vlan 39
      rd 10.1.1.3:10039
      route-target both 10039:10039
      redistribute learned
   !
   vlan 4
      rd 10.1.1.3:10004
      route-target both 10004:10004
      redistribute learned
   !
   vlan 40
      rd 10.1.1.3:10040
      route-target both 10040:10040
      redistribute learned
   !
   vlan 41
      rd 10.1.1.3:10041
      route-target both 10041:10041
      redistribute learned
   !
   vlan 42
      rd 10.1.1.3:10042
      route-target both 10042:10042
      redistribute learned
   !
   vlan 43
      rd 10.1.1.3:10043
      route-target both 10043:10043
      redistribute learned
   !
   vlan 44
      rd 10.1.1.3:10044
      route-target both 10044:10044
      redistribute learned
   !
   vlan 45
      rd 10.1.1.3:10045
      route-target both 10045:10045
      redistribute learned
   !
   vlan 46
      rd 10.1.1.3:10046
      route-target both 10046:10046
      redistribute learned
   !
   vlan 47
      rd 10.1.1.3:10047
      route-target both 10047:10047
      redistribute learned
   !
   vlan 48
      rd 10.1.1.3:10048
      route-target both 10048:10048
      redistribute learned
   !
   vlan 49
      rd 10.1.1.3:10049
      route-target both 10049:10049
      redistribute learned
   !
   vlan 5
      rd 10.1.1.3:10005
      route-target both 10005:10005
      redistribute learned
   !
   vlan 50
      rd 10.1.1.3:10050
      route-target both 10050:10050
      redistribute learned
   !
   vlan 51
      rd 10.1.1.3:10051
      route-target both 10051:10051
      redistribute learned
   !
   vlan 52
      rd 10.1.1.3:10052
      route-target both 10052:10052
      redistribute learned
   !
   vlan 53
      rd 10.1.1.3:10053
      route-target both 10053:10053
      redistribute learned
   !
   vlan 54
      rd 10.1.1.3:10054
      route-target both 10054:10054
      redistribute learned
   !
   vlan 55
      rd 10.1.1.3:10055
      route-target both 10055:10055
      redistribute learned
   !
   vlan 56
      rd 10.1.1.3:10056
      route-target both 10056:10056
      redistribute learned
   !
   vlan 57
      rd 10.1.1.3:10057
      route-target both 10057:10057
      redistribute learned
   !
   vlan 58
      rd 10.1.1.3:10058
      route-target both 10058:10058
      redistribute learned
   !
   vlan 59
      rd 10.1.1.3:10059
      route-target both 10059:10059
      redistribute learned
   !
   vlan 6
      rd 10.1.1.3:10006
      route-target both 10006:10006
      redistribute learned
   !
   vlan 60
      rd 10.1.1.3:10060
      route-target both 10060:10060
      redistribute learned
   !
   vlan 61
      rd 10.1.1.3:10061
      route-target both 10061:10061
      redistribute learned
   !
   vlan 62
      rd 10.1.1.3:10062
      route-target both 10062:10062
      redistribute learned
   !
   vlan 63
      rd 10.1.1.3:10063
      route-target both 10063:10063
      redistribute learned
   !
   vlan 64
      rd 10.1.1.3:10064
      route-target both 10064:10064
      redistribute learned
   !
   vlan 7
      rd 10.1.1.3:10007
      route-target both 10007:10007
      redistribute learned
   !
   vlan 8
      rd 10.1.1.3:10008
      route-target both 10008:10008
      redistribute learned
   !
   vlan 9
      rd 10.1.1.3:10009
      route-target both 10009:10009
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
      rd 10.1.1.3:254
      route-target import evpn 254:254
      route-target export evpn 254:254
      router-id 10.1.1.3
      neighbor 10.1.1.129 remote-as 65000
      neighbor 10.1.1.129 peer group IPv4-UNDERLAY-PEERS
      neighbor 10.1.1.129 description r1-site1-wan1_Ethernet1/5.254_vrf_customer1
      neighbor 10.1.1.131 remote-as 65000
      neighbor 10.1.1.131 peer group IPv4-UNDERLAY-PEERS
      neighbor 10.1.1.131 description r1-site1-wan2_Ethernet1/5.254_vrf_customer1
      redistribute connected
   !
   vrf customer2
      rd 10.1.1.3:2
      route-target import evpn 2:2
      route-target export evpn 2:2
      router-id 10.1.1.3
      neighbor 10.1.1.129 remote-as 65000
      neighbor 10.1.1.129 peer group IPv4-UNDERLAY-PEERS
      neighbor 10.1.1.129 description r1-site1-wan1_Ethernet1/5.2_vrf_customer2
      neighbor 10.1.1.131 remote-as 65000
      neighbor 10.1.1.131 peer group IPv4-UNDERLAY-PEERS
      neighbor 10.1.1.131 description r1-site1-wan2_Ethernet1/5.2_vrf_customer2
      redistribute connected
   !
   vrf customer3
      rd 10.1.1.3:3
      route-target import evpn 3:3
      route-target export evpn 3:3
      router-id 10.1.1.3
      neighbor 10.1.1.129 remote-as 65000
      neighbor 10.1.1.129 peer group IPv4-UNDERLAY-PEERS
      neighbor 10.1.1.129 description r1-site1-wan1_Ethernet1/5.3_vrf_customer3
      neighbor 10.1.1.131 remote-as 65000
      neighbor 10.1.1.131 peer group IPv4-UNDERLAY-PEERS
      neighbor 10.1.1.131 description r1-site1-wan2_Ethernet1/5.3_vrf_customer3
      redistribute connected
   !
   vrf customer4
      rd 10.1.1.3:4
      route-target import evpn 4:4
      route-target export evpn 4:4
      router-id 10.1.1.3
      neighbor 10.1.1.129 remote-as 65000
      neighbor 10.1.1.129 peer group IPv4-UNDERLAY-PEERS
      neighbor 10.1.1.129 description r1-site1-wan1_Ethernet1/5.4_vrf_customer4
      neighbor 10.1.1.131 remote-as 65000
      neighbor 10.1.1.131 peer group IPv4-UNDERLAY-PEERS
      neighbor 10.1.1.131 description r1-site1-wan2_Ethernet1/5.4_vrf_customer4
      redistribute connected
   !
   vrf customer5
      rd 10.1.1.3:5
      route-target import evpn 5:5
      route-target export evpn 5:5
      router-id 10.1.1.3
      neighbor 10.1.1.129 remote-as 65000
      neighbor 10.1.1.129 peer group IPv4-UNDERLAY-PEERS
      neighbor 10.1.1.129 description r1-site1-wan1_Ethernet1/5.5_vrf_customer5
      neighbor 10.1.1.131 remote-as 65000
      neighbor 10.1.1.131 peer group IPv4-UNDERLAY-PEERS
      neighbor 10.1.1.131 description r1-site1-wan2_Ethernet1/5.5_vrf_customer5
      redistribute connected
   !
   vrf customer6
      rd 10.1.1.3:6
      route-target import evpn 6:6
      route-target export evpn 6:6
      router-id 10.1.1.3
      neighbor 10.1.1.129 remote-as 65000
      neighbor 10.1.1.129 peer group IPv4-UNDERLAY-PEERS
      neighbor 10.1.1.129 description r1-site1-wan1_Ethernet1/5.6_vrf_customer6
      neighbor 10.1.1.131 remote-as 65000
      neighbor 10.1.1.131 peer group IPv4-UNDERLAY-PEERS
      neighbor 10.1.1.131 description r1-site1-wan2_Ethernet1/5.6_vrf_customer6
      redistribute connected
   !
   vrf customer7
      rd 10.1.1.3:7
      route-target import evpn 7:7
      route-target export evpn 7:7
      router-id 10.1.1.3
      neighbor 10.1.1.129 remote-as 65000
      neighbor 10.1.1.129 peer group IPv4-UNDERLAY-PEERS
      neighbor 10.1.1.129 description r1-site1-wan1_Ethernet1/5.7_vrf_customer7
      neighbor 10.1.1.131 remote-as 65000
      neighbor 10.1.1.131 peer group IPv4-UNDERLAY-PEERS
      neighbor 10.1.1.131 description r1-site1-wan2_Ethernet1/5.7_vrf_customer7
      redistribute connected
   !
   vrf customer8
      rd 10.1.1.3:8
      route-target import evpn 8:8
      route-target export evpn 8:8
      router-id 10.1.1.3
      neighbor 10.1.1.129 remote-as 65000
      neighbor 10.1.1.129 peer group IPv4-UNDERLAY-PEERS
      neighbor 10.1.1.129 description r1-site1-wan1_Ethernet1/5.8_vrf_customer8
      neighbor 10.1.1.131 remote-as 65000
      neighbor 10.1.1.131 peer group IPv4-UNDERLAY-PEERS
      neighbor 10.1.1.131 description r1-site1-wan2_Ethernet1/5.8_vrf_customer8
      redistribute connected
   !
   vrf customer9
      rd 10.1.1.3:9
      route-target import evpn 9:9
      route-target export evpn 9:9
      router-id 10.1.1.3
      neighbor 10.1.1.129 remote-as 65000
      neighbor 10.1.1.129 peer group IPv4-UNDERLAY-PEERS
      neighbor 10.1.1.129 description r1-site1-wan1_Ethernet1/5.9_vrf_customer9
      neighbor 10.1.1.131 remote-as 65000
      neighbor 10.1.1.131 peer group IPv4-UNDERLAY-PEERS
      neighbor 10.1.1.131 description r1-site1-wan2_Ethernet1/5.9_vrf_customer9
      redistribute connected
   !
   vrf customer10
      rd 10.1.1.3:10
      route-target import evpn 10:10
      route-target export evpn 10:10
      router-id 10.1.1.3
      neighbor 10.1.1.129 remote-as 65000
      neighbor 10.1.1.129 peer group IPv4-UNDERLAY-PEERS
      neighbor 10.1.1.129 description r1-site1-wan1_Ethernet1/5.10_vrf_customer10
      neighbor 10.1.1.131 remote-as 65000
      neighbor 10.1.1.131 peer group IPv4-UNDERLAY-PEERS
      neighbor 10.1.1.131 description r1-site1-wan2_Ethernet1/5.10_vrf_customer10
      redistribute connected
   !
   vrf customer11
      rd 10.1.1.3:11
      route-target import evpn 11:11
      route-target export evpn 11:11
      router-id 10.1.1.3
      neighbor 10.1.1.129 remote-as 65000
      neighbor 10.1.1.129 peer group IPv4-UNDERLAY-PEERS
      neighbor 10.1.1.129 description r1-site1-wan1_Ethernet1/5.11_vrf_customer11
      neighbor 10.1.1.131 remote-as 65000
      neighbor 10.1.1.131 peer group IPv4-UNDERLAY-PEERS
      neighbor 10.1.1.131 description r1-site1-wan2_Ethernet1/5.11_vrf_customer11
      redistribute connected
   !
   vrf customer12
      rd 10.1.1.3:12
      route-target import evpn 12:12
      route-target export evpn 12:12
      router-id 10.1.1.3
      neighbor 10.1.1.129 remote-as 65000
      neighbor 10.1.1.129 peer group IPv4-UNDERLAY-PEERS
      neighbor 10.1.1.129 description r1-site1-wan1_Ethernet1/5.12_vrf_customer12
      neighbor 10.1.1.131 remote-as 65000
      neighbor 10.1.1.131 peer group IPv4-UNDERLAY-PEERS
      neighbor 10.1.1.131 description r1-site1-wan2_Ethernet1/5.12_vrf_customer12
      redistribute connected
   !
   vrf customer13
      rd 10.1.1.3:13
      route-target import evpn 13:13
      route-target export evpn 13:13
      router-id 10.1.1.3
      neighbor 10.1.1.129 remote-as 65000
      neighbor 10.1.1.129 peer group IPv4-UNDERLAY-PEERS
      neighbor 10.1.1.129 description r1-site1-wan1_Ethernet1/5.13_vrf_customer13
      neighbor 10.1.1.131 remote-as 65000
      neighbor 10.1.1.131 peer group IPv4-UNDERLAY-PEERS
      neighbor 10.1.1.131 description r1-site1-wan2_Ethernet1/5.13_vrf_customer13
      redistribute connected
   !
   vrf customer14
      rd 10.1.1.3:14
      route-target import evpn 14:14
      route-target export evpn 14:14
      router-id 10.1.1.3
      neighbor 10.1.1.129 remote-as 65000
      neighbor 10.1.1.129 peer group IPv4-UNDERLAY-PEERS
      neighbor 10.1.1.129 description r1-site1-wan1_Ethernet1/5.14_vrf_customer14
      neighbor 10.1.1.131 remote-as 65000
      neighbor 10.1.1.131 peer group IPv4-UNDERLAY-PEERS
      neighbor 10.1.1.131 description r1-site1-wan2_Ethernet1/5.14_vrf_customer14
      redistribute connected
   !
   vrf customer15
      rd 10.1.1.3:15
      route-target import evpn 15:15
      route-target export evpn 15:15
      router-id 10.1.1.3
      neighbor 10.1.1.129 remote-as 65000
      neighbor 10.1.1.129 peer group IPv4-UNDERLAY-PEERS
      neighbor 10.1.1.129 description r1-site1-wan1_Ethernet1/5.15_vrf_customer15
      neighbor 10.1.1.131 remote-as 65000
      neighbor 10.1.1.131 peer group IPv4-UNDERLAY-PEERS
      neighbor 10.1.1.131 description r1-site1-wan2_Ethernet1/5.15_vrf_customer15
      redistribute connected
   !
   vrf customer16
      rd 10.1.1.3:16
      route-target import evpn 16:16
      route-target export evpn 16:16
      router-id 10.1.1.3
      neighbor 10.1.1.129 remote-as 65000
      neighbor 10.1.1.129 peer group IPv4-UNDERLAY-PEERS
      neighbor 10.1.1.129 description r1-site1-wan1_Ethernet1/5.16_vrf_customer16
      neighbor 10.1.1.131 remote-as 65000
      neighbor 10.1.1.131 peer group IPv4-UNDERLAY-PEERS
      neighbor 10.1.1.131 description r1-site1-wan2_Ethernet1/5.16_vrf_customer16
      redistribute connected
   !
   vrf customer17
      rd 10.1.1.3:17
      route-target import evpn 17:17
      route-target export evpn 17:17
      router-id 10.1.1.3
      neighbor 10.1.1.129 remote-as 65000
      neighbor 10.1.1.129 peer group IPv4-UNDERLAY-PEERS
      neighbor 10.1.1.129 description r1-site1-wan1_Ethernet1/5.17_vrf_customer17
      neighbor 10.1.1.131 remote-as 65000
      neighbor 10.1.1.131 peer group IPv4-UNDERLAY-PEERS
      neighbor 10.1.1.131 description r1-site1-wan2_Ethernet1/5.17_vrf_customer17
      redistribute connected
   !
   vrf customer18
      rd 10.1.1.3:18
      route-target import evpn 18:18
      route-target export evpn 18:18
      router-id 10.1.1.3
      neighbor 10.1.1.129 remote-as 65000
      neighbor 10.1.1.129 peer group IPv4-UNDERLAY-PEERS
      neighbor 10.1.1.129 description r1-site1-wan1_Ethernet1/5.18_vrf_customer18
      neighbor 10.1.1.131 remote-as 65000
      neighbor 10.1.1.131 peer group IPv4-UNDERLAY-PEERS
      neighbor 10.1.1.131 description r1-site1-wan2_Ethernet1/5.18_vrf_customer18
      redistribute connected
   !
   vrf customer19
      rd 10.1.1.3:19
      route-target import evpn 19:19
      route-target export evpn 19:19
      router-id 10.1.1.3
      neighbor 10.1.1.129 remote-as 65000
      neighbor 10.1.1.129 peer group IPv4-UNDERLAY-PEERS
      neighbor 10.1.1.129 description r1-site1-wan1_Ethernet1/5.19_vrf_customer19
      neighbor 10.1.1.131 remote-as 65000
      neighbor 10.1.1.131 peer group IPv4-UNDERLAY-PEERS
      neighbor 10.1.1.131 description r1-site1-wan2_Ethernet1/5.19_vrf_customer19
      redistribute connected
   !
   vrf customer20
      rd 10.1.1.3:20
      route-target import evpn 20:20
      route-target export evpn 20:20
      router-id 10.1.1.3
      neighbor 10.1.1.129 remote-as 65000
      neighbor 10.1.1.129 peer group IPv4-UNDERLAY-PEERS
      neighbor 10.1.1.129 description r1-site1-wan1_Ethernet1/5.20_vrf_customer20
      neighbor 10.1.1.131 remote-as 65000
      neighbor 10.1.1.131 peer group IPv4-UNDERLAY-PEERS
      neighbor 10.1.1.131 description r1-site1-wan2_Ethernet1/5.20_vrf_customer20
      redistribute connected
   !
   vrf customer21
      rd 10.1.1.3:21
      route-target import evpn 21:21
      route-target export evpn 21:21
      router-id 10.1.1.3
      neighbor 10.1.1.129 remote-as 65000
      neighbor 10.1.1.129 peer group IPv4-UNDERLAY-PEERS
      neighbor 10.1.1.129 description r1-site1-wan1_Ethernet1/5.21_vrf_customer21
      neighbor 10.1.1.131 remote-as 65000
      neighbor 10.1.1.131 peer group IPv4-UNDERLAY-PEERS
      neighbor 10.1.1.131 description r1-site1-wan2_Ethernet1/5.21_vrf_customer21
      redistribute connected
   !
   vrf customer22
      rd 10.1.1.3:22
      route-target import evpn 22:22
      route-target export evpn 22:22
      router-id 10.1.1.3
      neighbor 10.1.1.129 remote-as 65000
      neighbor 10.1.1.129 peer group IPv4-UNDERLAY-PEERS
      neighbor 10.1.1.129 description r1-site1-wan1_Ethernet1/5.22_vrf_customer22
      neighbor 10.1.1.131 remote-as 65000
      neighbor 10.1.1.131 peer group IPv4-UNDERLAY-PEERS
      neighbor 10.1.1.131 description r1-site1-wan2_Ethernet1/5.22_vrf_customer22
      redistribute connected
   !
   vrf customer23
      rd 10.1.1.3:23
      route-target import evpn 23:23
      route-target export evpn 23:23
      router-id 10.1.1.3
      neighbor 10.1.1.129 remote-as 65000
      neighbor 10.1.1.129 peer group IPv4-UNDERLAY-PEERS
      neighbor 10.1.1.129 description r1-site1-wan1_Ethernet1/5.23_vrf_customer23
      neighbor 10.1.1.131 remote-as 65000
      neighbor 10.1.1.131 peer group IPv4-UNDERLAY-PEERS
      neighbor 10.1.1.131 description r1-site1-wan2_Ethernet1/5.23_vrf_customer23
      redistribute connected
   !
   vrf customer24
      rd 10.1.1.3:24
      route-target import evpn 24:24
      route-target export evpn 24:24
      router-id 10.1.1.3
      neighbor 10.1.1.129 remote-as 65000
      neighbor 10.1.1.129 peer group IPv4-UNDERLAY-PEERS
      neighbor 10.1.1.129 description r1-site1-wan1_Ethernet1/5.24_vrf_customer24
      neighbor 10.1.1.131 remote-as 65000
      neighbor 10.1.1.131 peer group IPv4-UNDERLAY-PEERS
      neighbor 10.1.1.131 description r1-site1-wan2_Ethernet1/5.24_vrf_customer24
      redistribute connected
   !
   vrf customer25
      rd 10.1.1.3:25
      route-target import evpn 25:25
      route-target export evpn 25:25
      router-id 10.1.1.3
      neighbor 10.1.1.129 remote-as 65000
      neighbor 10.1.1.129 peer group IPv4-UNDERLAY-PEERS
      neighbor 10.1.1.129 description r1-site1-wan1_Ethernet1/5.25_vrf_customer25
      neighbor 10.1.1.131 remote-as 65000
      neighbor 10.1.1.131 peer group IPv4-UNDERLAY-PEERS
      neighbor 10.1.1.131 description r1-site1-wan2_Ethernet1/5.25_vrf_customer25
      redistribute connected
   !
   vrf customer26
      rd 10.1.1.3:26
      route-target import evpn 26:26
      route-target export evpn 26:26
      router-id 10.1.1.3
      neighbor 10.1.1.129 remote-as 65000
      neighbor 10.1.1.129 peer group IPv4-UNDERLAY-PEERS
      neighbor 10.1.1.129 description r1-site1-wan1_Ethernet1/5.26_vrf_customer26
      neighbor 10.1.1.131 remote-as 65000
      neighbor 10.1.1.131 peer group IPv4-UNDERLAY-PEERS
      neighbor 10.1.1.131 description r1-site1-wan2_Ethernet1/5.26_vrf_customer26
      redistribute connected
   !
   vrf customer27
      rd 10.1.1.3:27
      route-target import evpn 27:27
      route-target export evpn 27:27
      router-id 10.1.1.3
      neighbor 10.1.1.129 remote-as 65000
      neighbor 10.1.1.129 peer group IPv4-UNDERLAY-PEERS
      neighbor 10.1.1.129 description r1-site1-wan1_Ethernet1/5.27_vrf_customer27
      neighbor 10.1.1.131 remote-as 65000
      neighbor 10.1.1.131 peer group IPv4-UNDERLAY-PEERS
      neighbor 10.1.1.131 description r1-site1-wan2_Ethernet1/5.27_vrf_customer27
      redistribute connected
   !
   vrf customer28
      rd 10.1.1.3:28
      route-target import evpn 28:28
      route-target export evpn 28:28
      router-id 10.1.1.3
      neighbor 10.1.1.129 remote-as 65000
      neighbor 10.1.1.129 peer group IPv4-UNDERLAY-PEERS
      neighbor 10.1.1.129 description r1-site1-wan1_Ethernet1/5.28_vrf_customer28
      neighbor 10.1.1.131 remote-as 65000
      neighbor 10.1.1.131 peer group IPv4-UNDERLAY-PEERS
      neighbor 10.1.1.131 description r1-site1-wan2_Ethernet1/5.28_vrf_customer28
      redistribute connected
   !
   vrf customer29
      rd 10.1.1.3:29
      route-target import evpn 29:29
      route-target export evpn 29:29
      router-id 10.1.1.3
      neighbor 10.1.1.129 remote-as 65000
      neighbor 10.1.1.129 peer group IPv4-UNDERLAY-PEERS
      neighbor 10.1.1.129 description r1-site1-wan1_Ethernet1/5.29_vrf_customer29
      neighbor 10.1.1.131 remote-as 65000
      neighbor 10.1.1.131 peer group IPv4-UNDERLAY-PEERS
      neighbor 10.1.1.131 description r1-site1-wan2_Ethernet1/5.29_vrf_customer29
      redistribute connected
   !
   vrf customer30
      rd 10.1.1.3:30
      route-target import evpn 30:30
      route-target export evpn 30:30
      router-id 10.1.1.3
      neighbor 10.1.1.129 remote-as 65000
      neighbor 10.1.1.129 peer group IPv4-UNDERLAY-PEERS
      neighbor 10.1.1.129 description r1-site1-wan1_Ethernet1/5.30_vrf_customer30
      neighbor 10.1.1.131 remote-as 65000
      neighbor 10.1.1.131 peer group IPv4-UNDERLAY-PEERS
      neighbor 10.1.1.131 description r1-site1-wan2_Ethernet1/5.30_vrf_customer30
      redistribute connected
   !
   vrf customer31
      rd 10.1.1.3:31
      route-target import evpn 31:31
      route-target export evpn 31:31
      router-id 10.1.1.3
      neighbor 10.1.1.129 remote-as 65000
      neighbor 10.1.1.129 peer group IPv4-UNDERLAY-PEERS
      neighbor 10.1.1.129 description r1-site1-wan1_Ethernet1/5.31_vrf_customer31
      neighbor 10.1.1.131 remote-as 65000
      neighbor 10.1.1.131 peer group IPv4-UNDERLAY-PEERS
      neighbor 10.1.1.131 description r1-site1-wan2_Ethernet1/5.31_vrf_customer31
      redistribute connected
   !
   vrf customer32
      rd 10.1.1.3:32
      route-target import evpn 32:32
      route-target export evpn 32:32
      router-id 10.1.1.3
      neighbor 10.1.1.129 remote-as 65000
      neighbor 10.1.1.129 peer group IPv4-UNDERLAY-PEERS
      neighbor 10.1.1.129 description r1-site1-wan1_Ethernet1/5.32_vrf_customer32
      neighbor 10.1.1.131 remote-as 65000
      neighbor 10.1.1.131 peer group IPv4-UNDERLAY-PEERS
      neighbor 10.1.1.131 description r1-site1-wan2_Ethernet1/5.32_vrf_customer32
      redistribute connected
   !
   vrf customer33
      rd 10.1.1.3:33
      route-target import evpn 33:33
      route-target export evpn 33:33
      router-id 10.1.1.3
      neighbor 10.1.1.129 remote-as 65000
      neighbor 10.1.1.129 peer group IPv4-UNDERLAY-PEERS
      neighbor 10.1.1.129 description r1-site1-wan1_Ethernet1/5.33_vrf_customer33
      neighbor 10.1.1.131 remote-as 65000
      neighbor 10.1.1.131 peer group IPv4-UNDERLAY-PEERS
      neighbor 10.1.1.131 description r1-site1-wan2_Ethernet1/5.33_vrf_customer33
      redistribute connected
   !
   vrf customer34
      rd 10.1.1.3:34
      route-target import evpn 34:34
      route-target export evpn 34:34
      router-id 10.1.1.3
      neighbor 10.1.1.129 remote-as 65000
      neighbor 10.1.1.129 peer group IPv4-UNDERLAY-PEERS
      neighbor 10.1.1.129 description r1-site1-wan1_Ethernet1/5.34_vrf_customer34
      neighbor 10.1.1.131 remote-as 65000
      neighbor 10.1.1.131 peer group IPv4-UNDERLAY-PEERS
      neighbor 10.1.1.131 description r1-site1-wan2_Ethernet1/5.34_vrf_customer34
      redistribute connected
   !
   vrf customer35
      rd 10.1.1.3:35
      route-target import evpn 35:35
      route-target export evpn 35:35
      router-id 10.1.1.3
      neighbor 10.1.1.129 remote-as 65000
      neighbor 10.1.1.129 peer group IPv4-UNDERLAY-PEERS
      neighbor 10.1.1.129 description r1-site1-wan1_Ethernet1/5.35_vrf_customer35
      neighbor 10.1.1.131 remote-as 65000
      neighbor 10.1.1.131 peer group IPv4-UNDERLAY-PEERS
      neighbor 10.1.1.131 description r1-site1-wan2_Ethernet1/5.35_vrf_customer35
      redistribute connected
   !
   vrf customer36
      rd 10.1.1.3:36
      route-target import evpn 36:36
      route-target export evpn 36:36
      router-id 10.1.1.3
      neighbor 10.1.1.129 remote-as 65000
      neighbor 10.1.1.129 peer group IPv4-UNDERLAY-PEERS
      neighbor 10.1.1.129 description r1-site1-wan1_Ethernet1/5.36_vrf_customer36
      neighbor 10.1.1.131 remote-as 65000
      neighbor 10.1.1.131 peer group IPv4-UNDERLAY-PEERS
      neighbor 10.1.1.131 description r1-site1-wan2_Ethernet1/5.36_vrf_customer36
      redistribute connected
   !
   vrf customer37
      rd 10.1.1.3:37
      route-target import evpn 37:37
      route-target export evpn 37:37
      router-id 10.1.1.3
      neighbor 10.1.1.129 remote-as 65000
      neighbor 10.1.1.129 peer group IPv4-UNDERLAY-PEERS
      neighbor 10.1.1.129 description r1-site1-wan1_Ethernet1/5.37_vrf_customer37
      neighbor 10.1.1.131 remote-as 65000
      neighbor 10.1.1.131 peer group IPv4-UNDERLAY-PEERS
      neighbor 10.1.1.131 description r1-site1-wan2_Ethernet1/5.37_vrf_customer37
      redistribute connected
   !
   vrf customer38
      rd 10.1.1.3:38
      route-target import evpn 38:38
      route-target export evpn 38:38
      router-id 10.1.1.3
      neighbor 10.1.1.129 remote-as 65000
      neighbor 10.1.1.129 peer group IPv4-UNDERLAY-PEERS
      neighbor 10.1.1.129 description r1-site1-wan1_Ethernet1/5.38_vrf_customer38
      neighbor 10.1.1.131 remote-as 65000
      neighbor 10.1.1.131 peer group IPv4-UNDERLAY-PEERS
      neighbor 10.1.1.131 description r1-site1-wan2_Ethernet1/5.38_vrf_customer38
      redistribute connected
   !
   vrf customer39
      rd 10.1.1.3:39
      route-target import evpn 39:39
      route-target export evpn 39:39
      router-id 10.1.1.3
      neighbor 10.1.1.129 remote-as 65000
      neighbor 10.1.1.129 peer group IPv4-UNDERLAY-PEERS
      neighbor 10.1.1.129 description r1-site1-wan1_Ethernet1/5.39_vrf_customer39
      neighbor 10.1.1.131 remote-as 65000
      neighbor 10.1.1.131 peer group IPv4-UNDERLAY-PEERS
      neighbor 10.1.1.131 description r1-site1-wan2_Ethernet1/5.39_vrf_customer39
      redistribute connected
   !
   vrf customer40
      rd 10.1.1.3:40
      route-target import evpn 40:40
      route-target export evpn 40:40
      router-id 10.1.1.3
      neighbor 10.1.1.129 remote-as 65000
      neighbor 10.1.1.129 peer group IPv4-UNDERLAY-PEERS
      neighbor 10.1.1.129 description r1-site1-wan1_Ethernet1/5.40_vrf_customer40
      neighbor 10.1.1.131 remote-as 65000
      neighbor 10.1.1.131 peer group IPv4-UNDERLAY-PEERS
      neighbor 10.1.1.131 description r1-site1-wan2_Ethernet1/5.40_vrf_customer40
      redistribute connected
   !
   vrf customer41
      rd 10.1.1.3:41
      route-target import evpn 41:41
      route-target export evpn 41:41
      router-id 10.1.1.3
      neighbor 10.1.1.129 remote-as 65000
      neighbor 10.1.1.129 peer group IPv4-UNDERLAY-PEERS
      neighbor 10.1.1.129 description r1-site1-wan1_Ethernet1/5.41_vrf_customer41
      neighbor 10.1.1.131 remote-as 65000
      neighbor 10.1.1.131 peer group IPv4-UNDERLAY-PEERS
      neighbor 10.1.1.131 description r1-site1-wan2_Ethernet1/5.41_vrf_customer41
      redistribute connected
   !
   vrf customer42
      rd 10.1.1.3:42
      route-target import evpn 42:42
      route-target export evpn 42:42
      router-id 10.1.1.3
      neighbor 10.1.1.129 remote-as 65000
      neighbor 10.1.1.129 peer group IPv4-UNDERLAY-PEERS
      neighbor 10.1.1.129 description r1-site1-wan1_Ethernet1/5.42_vrf_customer42
      neighbor 10.1.1.131 remote-as 65000
      neighbor 10.1.1.131 peer group IPv4-UNDERLAY-PEERS
      neighbor 10.1.1.131 description r1-site1-wan2_Ethernet1/5.42_vrf_customer42
      redistribute connected
   !
   vrf customer43
      rd 10.1.1.3:43
      route-target import evpn 43:43
      route-target export evpn 43:43
      router-id 10.1.1.3
      neighbor 10.1.1.129 remote-as 65000
      neighbor 10.1.1.129 peer group IPv4-UNDERLAY-PEERS
      neighbor 10.1.1.129 description r1-site1-wan1_Ethernet1/5.43_vrf_customer43
      neighbor 10.1.1.131 remote-as 65000
      neighbor 10.1.1.131 peer group IPv4-UNDERLAY-PEERS
      neighbor 10.1.1.131 description r1-site1-wan2_Ethernet1/5.43_vrf_customer43
      redistribute connected
   !
   vrf customer44
      rd 10.1.1.3:44
      route-target import evpn 44:44
      route-target export evpn 44:44
      router-id 10.1.1.3
      neighbor 10.1.1.129 remote-as 65000
      neighbor 10.1.1.129 peer group IPv4-UNDERLAY-PEERS
      neighbor 10.1.1.129 description r1-site1-wan1_Ethernet1/5.44_vrf_customer44
      neighbor 10.1.1.131 remote-as 65000
      neighbor 10.1.1.131 peer group IPv4-UNDERLAY-PEERS
      neighbor 10.1.1.131 description r1-site1-wan2_Ethernet1/5.44_vrf_customer44
      redistribute connected
   !
   vrf customer45
      rd 10.1.1.3:45
      route-target import evpn 45:45
      route-target export evpn 45:45
      router-id 10.1.1.3
      neighbor 10.1.1.129 remote-as 65000
      neighbor 10.1.1.129 peer group IPv4-UNDERLAY-PEERS
      neighbor 10.1.1.129 description r1-site1-wan1_Ethernet1/5.45_vrf_customer45
      neighbor 10.1.1.131 remote-as 65000
      neighbor 10.1.1.131 peer group IPv4-UNDERLAY-PEERS
      neighbor 10.1.1.131 description r1-site1-wan2_Ethernet1/5.45_vrf_customer45
      redistribute connected
   !
   vrf customer46
      rd 10.1.1.3:46
      route-target import evpn 46:46
      route-target export evpn 46:46
      router-id 10.1.1.3
      neighbor 10.1.1.129 remote-as 65000
      neighbor 10.1.1.129 peer group IPv4-UNDERLAY-PEERS
      neighbor 10.1.1.129 description r1-site1-wan1_Ethernet1/5.46_vrf_customer46
      neighbor 10.1.1.131 remote-as 65000
      neighbor 10.1.1.131 peer group IPv4-UNDERLAY-PEERS
      neighbor 10.1.1.131 description r1-site1-wan2_Ethernet1/5.46_vrf_customer46
      redistribute connected
   !
   vrf customer47
      rd 10.1.1.3:47
      route-target import evpn 47:47
      route-target export evpn 47:47
      router-id 10.1.1.3
      neighbor 10.1.1.129 remote-as 65000
      neighbor 10.1.1.129 peer group IPv4-UNDERLAY-PEERS
      neighbor 10.1.1.129 description r1-site1-wan1_Ethernet1/5.47_vrf_customer47
      neighbor 10.1.1.131 remote-as 65000
      neighbor 10.1.1.131 peer group IPv4-UNDERLAY-PEERS
      neighbor 10.1.1.131 description r1-site1-wan2_Ethernet1/5.47_vrf_customer47
      redistribute connected
   !
   vrf customer48
      rd 10.1.1.3:48
      route-target import evpn 48:48
      route-target export evpn 48:48
      router-id 10.1.1.3
      neighbor 10.1.1.129 remote-as 65000
      neighbor 10.1.1.129 peer group IPv4-UNDERLAY-PEERS
      neighbor 10.1.1.129 description r1-site1-wan1_Ethernet1/5.48_vrf_customer48
      neighbor 10.1.1.131 remote-as 65000
      neighbor 10.1.1.131 peer group IPv4-UNDERLAY-PEERS
      neighbor 10.1.1.131 description r1-site1-wan2_Ethernet1/5.48_vrf_customer48
      redistribute connected
   !
   vrf customer49
      rd 10.1.1.3:49
      route-target import evpn 49:49
      route-target export evpn 49:49
      router-id 10.1.1.3
      neighbor 10.1.1.129 remote-as 65000
      neighbor 10.1.1.129 peer group IPv4-UNDERLAY-PEERS
      neighbor 10.1.1.129 description r1-site1-wan1_Ethernet1/5.49_vrf_customer49
      neighbor 10.1.1.131 remote-as 65000
      neighbor 10.1.1.131 peer group IPv4-UNDERLAY-PEERS
      neighbor 10.1.1.131 description r1-site1-wan2_Ethernet1/5.49_vrf_customer49
      redistribute connected
   !
   vrf customer50
      rd 10.1.1.3:50
      route-target import evpn 50:50
      route-target export evpn 50:50
      router-id 10.1.1.3
      neighbor 10.1.1.129 remote-as 65000
      neighbor 10.1.1.129 peer group IPv4-UNDERLAY-PEERS
      neighbor 10.1.1.129 description r1-site1-wan1_Ethernet1/5.50_vrf_customer50
      neighbor 10.1.1.131 remote-as 65000
      neighbor 10.1.1.131 peer group IPv4-UNDERLAY-PEERS
      neighbor 10.1.1.131 description r1-site1-wan2_Ethernet1/5.50_vrf_customer50
      redistribute connected
   !
   vrf customer51
      rd 10.1.1.3:51
      route-target import evpn 51:51
      route-target export evpn 51:51
      router-id 10.1.1.3
      neighbor 10.1.1.129 remote-as 65000
      neighbor 10.1.1.129 peer group IPv4-UNDERLAY-PEERS
      neighbor 10.1.1.129 description r1-site1-wan1_Ethernet1/5.51_vrf_customer51
      neighbor 10.1.1.131 remote-as 65000
      neighbor 10.1.1.131 peer group IPv4-UNDERLAY-PEERS
      neighbor 10.1.1.131 description r1-site1-wan2_Ethernet1/5.51_vrf_customer51
      redistribute connected
   !
   vrf customer52
      rd 10.1.1.3:52
      route-target import evpn 52:52
      route-target export evpn 52:52
      router-id 10.1.1.3
      neighbor 10.1.1.129 remote-as 65000
      neighbor 10.1.1.129 peer group IPv4-UNDERLAY-PEERS
      neighbor 10.1.1.129 description r1-site1-wan1_Ethernet1/5.52_vrf_customer52
      neighbor 10.1.1.131 remote-as 65000
      neighbor 10.1.1.131 peer group IPv4-UNDERLAY-PEERS
      neighbor 10.1.1.131 description r1-site1-wan2_Ethernet1/5.52_vrf_customer52
      redistribute connected
   !
   vrf customer53
      rd 10.1.1.3:53
      route-target import evpn 53:53
      route-target export evpn 53:53
      router-id 10.1.1.3
      neighbor 10.1.1.129 remote-as 65000
      neighbor 10.1.1.129 peer group IPv4-UNDERLAY-PEERS
      neighbor 10.1.1.129 description r1-site1-wan1_Ethernet1/5.53_vrf_customer53
      neighbor 10.1.1.131 remote-as 65000
      neighbor 10.1.1.131 peer group IPv4-UNDERLAY-PEERS
      neighbor 10.1.1.131 description r1-site1-wan2_Ethernet1/5.53_vrf_customer53
      redistribute connected
   !
   vrf customer54
      rd 10.1.1.3:54
      route-target import evpn 54:54
      route-target export evpn 54:54
      router-id 10.1.1.3
      neighbor 10.1.1.129 remote-as 65000
      neighbor 10.1.1.129 peer group IPv4-UNDERLAY-PEERS
      neighbor 10.1.1.129 description r1-site1-wan1_Ethernet1/5.54_vrf_customer54
      neighbor 10.1.1.131 remote-as 65000
      neighbor 10.1.1.131 peer group IPv4-UNDERLAY-PEERS
      neighbor 10.1.1.131 description r1-site1-wan2_Ethernet1/5.54_vrf_customer54
      redistribute connected
   !
   vrf customer55
      rd 10.1.1.3:55
      route-target import evpn 55:55
      route-target export evpn 55:55
      router-id 10.1.1.3
      neighbor 10.1.1.129 remote-as 65000
      neighbor 10.1.1.129 peer group IPv4-UNDERLAY-PEERS
      neighbor 10.1.1.129 description r1-site1-wan1_Ethernet1/5.55_vrf_customer55
      neighbor 10.1.1.131 remote-as 65000
      neighbor 10.1.1.131 peer group IPv4-UNDERLAY-PEERS
      neighbor 10.1.1.131 description r1-site1-wan2_Ethernet1/5.55_vrf_customer55
      redistribute connected
   !
   vrf customer56
      rd 10.1.1.3:56
      route-target import evpn 56:56
      route-target export evpn 56:56
      router-id 10.1.1.3
      neighbor 10.1.1.129 remote-as 65000
      neighbor 10.1.1.129 peer group IPv4-UNDERLAY-PEERS
      neighbor 10.1.1.129 description r1-site1-wan1_Ethernet1/5.56_vrf_customer56
      neighbor 10.1.1.131 remote-as 65000
      neighbor 10.1.1.131 peer group IPv4-UNDERLAY-PEERS
      neighbor 10.1.1.131 description r1-site1-wan2_Ethernet1/5.56_vrf_customer56
      redistribute connected
   !
   vrf customer57
      rd 10.1.1.3:57
      route-target import evpn 57:57
      route-target export evpn 57:57
      router-id 10.1.1.3
      neighbor 10.1.1.129 remote-as 65000
      neighbor 10.1.1.129 peer group IPv4-UNDERLAY-PEERS
      neighbor 10.1.1.129 description r1-site1-wan1_Ethernet1/5.57_vrf_customer57
      neighbor 10.1.1.131 remote-as 65000
      neighbor 10.1.1.131 peer group IPv4-UNDERLAY-PEERS
      neighbor 10.1.1.131 description r1-site1-wan2_Ethernet1/5.57_vrf_customer57
      redistribute connected
   !
   vrf customer58
      rd 10.1.1.3:58
      route-target import evpn 58:58
      route-target export evpn 58:58
      router-id 10.1.1.3
      neighbor 10.1.1.129 remote-as 65000
      neighbor 10.1.1.129 peer group IPv4-UNDERLAY-PEERS
      neighbor 10.1.1.129 description r1-site1-wan1_Ethernet1/5.58_vrf_customer58
      neighbor 10.1.1.131 remote-as 65000
      neighbor 10.1.1.131 peer group IPv4-UNDERLAY-PEERS
      neighbor 10.1.1.131 description r1-site1-wan2_Ethernet1/5.58_vrf_customer58
      redistribute connected
   !
   vrf customer59
      rd 10.1.1.3:59
      route-target import evpn 59:59
      route-target export evpn 59:59
      router-id 10.1.1.3
      neighbor 10.1.1.129 remote-as 65000
      neighbor 10.1.1.129 peer group IPv4-UNDERLAY-PEERS
      neighbor 10.1.1.129 description r1-site1-wan1_Ethernet1/5.59_vrf_customer59
      neighbor 10.1.1.131 remote-as 65000
      neighbor 10.1.1.131 peer group IPv4-UNDERLAY-PEERS
      neighbor 10.1.1.131 description r1-site1-wan2_Ethernet1/5.59_vrf_customer59
      redistribute connected
   !
   vrf customer60
      rd 10.1.1.3:60
      route-target import evpn 60:60
      route-target export evpn 60:60
      router-id 10.1.1.3
      neighbor 10.1.1.129 remote-as 65000
      neighbor 10.1.1.129 peer group IPv4-UNDERLAY-PEERS
      neighbor 10.1.1.129 description r1-site1-wan1_Ethernet1/5.60_vrf_customer60
      neighbor 10.1.1.131 remote-as 65000
      neighbor 10.1.1.131 peer group IPv4-UNDERLAY-PEERS
      neighbor 10.1.1.131 description r1-site1-wan2_Ethernet1/5.60_vrf_customer60
      redistribute connected
   !
   vrf customer61
      rd 10.1.1.3:61
      route-target import evpn 61:61
      route-target export evpn 61:61
      router-id 10.1.1.3
      neighbor 10.1.1.129 remote-as 65000
      neighbor 10.1.1.129 peer group IPv4-UNDERLAY-PEERS
      neighbor 10.1.1.129 description r1-site1-wan1_Ethernet1/5.61_vrf_customer61
      neighbor 10.1.1.131 remote-as 65000
      neighbor 10.1.1.131 peer group IPv4-UNDERLAY-PEERS
      neighbor 10.1.1.131 description r1-site1-wan2_Ethernet1/5.61_vrf_customer61
      redistribute connected
   !
   vrf customer62
      rd 10.1.1.3:62
      route-target import evpn 62:62
      route-target export evpn 62:62
      router-id 10.1.1.3
      neighbor 10.1.1.129 remote-as 65000
      neighbor 10.1.1.129 peer group IPv4-UNDERLAY-PEERS
      neighbor 10.1.1.129 description r1-site1-wan1_Ethernet1/5.62_vrf_customer62
      neighbor 10.1.1.131 remote-as 65000
      neighbor 10.1.1.131 peer group IPv4-UNDERLAY-PEERS
      neighbor 10.1.1.131 description r1-site1-wan2_Ethernet1/5.62_vrf_customer62
      redistribute connected
   !
   vrf customer63
      rd 10.1.1.3:63
      route-target import evpn 63:63
      route-target export evpn 63:63
      router-id 10.1.1.3
      neighbor 10.1.1.129 remote-as 65000
      neighbor 10.1.1.129 peer group IPv4-UNDERLAY-PEERS
      neighbor 10.1.1.129 description r1-site1-wan1_Ethernet1/5.63_vrf_customer63
      neighbor 10.1.1.131 remote-as 65000
      neighbor 10.1.1.131 peer group IPv4-UNDERLAY-PEERS
      neighbor 10.1.1.131 description r1-site1-wan2_Ethernet1/5.63_vrf_customer63
      redistribute connected
   !
   vrf customer64
      rd 10.1.1.3:64
      route-target import evpn 64:64
      route-target export evpn 64:64
      router-id 10.1.1.3
      neighbor 10.1.1.129 remote-as 65000
      neighbor 10.1.1.129 peer group IPv4-UNDERLAY-PEERS
      neighbor 10.1.1.129 description r1-site1-wan1_Ethernet1/5.64_vrf_customer64
      neighbor 10.1.1.131 remote-as 65000
      neighbor 10.1.1.131 peer group IPv4-UNDERLAY-PEERS
      neighbor 10.1.1.131 description r1-site1-wan2_Ethernet1/5.64_vrf_customer64
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
| 10 | permit 10.1.1.0/26 eq 32 |
| 20 | permit 10.1.1.64/26 eq 32 |

#### Prefix-lists Device Configuration

```eos
!
ip prefix-list PL-LOOPBACKS-EVPN-OVERLAY
   seq 10 permit 10.1.1.0/26 eq 32
   seq 20 permit 10.1.1.64/26 eq 32
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
| customer7 | enabled |
| customer8 | enabled |
| customer9 | enabled |
| customer10 | enabled |
| customer11 | enabled |
| customer12 | enabled |
| customer13 | enabled |
| customer14 | enabled |
| customer15 | enabled |
| customer16 | enabled |
| customer17 | enabled |
| customer18 | enabled |
| customer19 | enabled |
| customer20 | enabled |
| customer21 | enabled |
| customer22 | enabled |
| customer23 | enabled |
| customer24 | enabled |
| customer25 | enabled |
| customer26 | enabled |
| customer27 | enabled |
| customer28 | enabled |
| customer29 | enabled |
| customer30 | enabled |
| customer31 | enabled |
| customer32 | enabled |
| customer33 | enabled |
| customer34 | enabled |
| customer35 | enabled |
| customer36 | enabled |
| customer37 | enabled |
| customer38 | enabled |
| customer39 | enabled |
| customer40 | enabled |
| customer41 | enabled |
| customer42 | enabled |
| customer43 | enabled |
| customer44 | enabled |
| customer45 | enabled |
| customer46 | enabled |
| customer47 | enabled |
| customer48 | enabled |
| customer49 | enabled |
| customer50 | enabled |
| customer51 | enabled |
| customer52 | enabled |
| customer53 | enabled |
| customer54 | enabled |
| customer55 | enabled |
| customer56 | enabled |
| customer57 | enabled |
| customer58 | enabled |
| customer59 | enabled |
| customer60 | enabled |
| customer61 | enabled |
| customer62 | enabled |
| customer63 | enabled |
| customer64 | enabled |
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
vrf instance customer7
!
vrf instance customer8
!
vrf instance customer9
!
vrf instance customer10
!
vrf instance customer11
!
vrf instance customer12
!
vrf instance customer13
!
vrf instance customer14
!
vrf instance customer15
!
vrf instance customer16
!
vrf instance customer17
!
vrf instance customer18
!
vrf instance customer19
!
vrf instance customer20
!
vrf instance customer21
!
vrf instance customer22
!
vrf instance customer23
!
vrf instance customer24
!
vrf instance customer25
!
vrf instance customer26
!
vrf instance customer27
!
vrf instance customer28
!
vrf instance customer29
!
vrf instance customer30
!
vrf instance customer31
!
vrf instance customer32
!
vrf instance customer33
!
vrf instance customer34
!
vrf instance customer35
!
vrf instance customer36
!
vrf instance customer37
!
vrf instance customer38
!
vrf instance customer39
!
vrf instance customer40
!
vrf instance customer41
!
vrf instance customer42
!
vrf instance customer43
!
vrf instance customer44
!
vrf instance customer45
!
vrf instance customer46
!
vrf instance customer47
!
vrf instance customer48
!
vrf instance customer49
!
vrf instance customer50
!
vrf instance customer51
!
vrf instance customer52
!
vrf instance customer53
!
vrf instance customer54
!
vrf instance customer55
!
vrf instance customer56
!
vrf instance customer57
!
vrf instance customer58
!
vrf instance customer59
!
vrf instance customer60
!
vrf instance customer61
!
vrf instance customer62
!
vrf instance customer63
!
vrf instance customer64
!
vrf instance MGMT
```
