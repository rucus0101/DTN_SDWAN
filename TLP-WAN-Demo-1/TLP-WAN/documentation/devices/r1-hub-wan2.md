# r1-hub-wan2

## Table of Contents

- [Management](#management)
  - [Agents](#agents)
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
  - [Flow Tracking](#flow-tracking)
- [Spanning Tree](#spanning-tree)
  - [Spanning Tree Summary](#spanning-tree-summary)
  - [Spanning Tree Device Configuration](#spanning-tree-device-configuration)
- [IP Security](#ip-security)
  - [IKE policies](#ike-policies)
  - [Security Association policies](#security-association-policies)
  - [IPSec profiles](#ipsec-profiles)
  - [Key controller](#key-controller)
  - [IP Security Device Configuration](#ip-security-device-configuration)
- [Interfaces](#interfaces)
  - [DPS Interfaces](#dps-interfaces)
  - [Ethernet Interfaces](#ethernet-interfaces)
  - [Loopback Interfaces](#loopback-interfaces)
  - [VXLAN Interface](#vxlan-interface)
- [Routing](#routing)
  - [Service Routing Protocols Model](#service-routing-protocols-model)
  - [IP Routing](#ip-routing)
  - [IPv6 Routing](#ipv6-routing)
  - [Static Routes](#static-routes)
  - [Router Adaptive Virtual Topology](#router-adaptive-virtual-topology)
  - [Router Traffic-Engineering](#router-traffic-engineering)
  - [Router BGP](#router-bgp)
- [BFD](#bfd)
  - [Router BFD](#router-bfd)
- [Filters](#filters)
  - [Prefix-lists](#prefix-lists)
  - [Route-maps](#route-maps)
  - [IP Extended Community Lists](#ip-extended-community-lists)
- [VRF Instances](#vrf-instances)
  - [VRF Instances Summary](#vrf-instances-summary)
  - [VRF Instances Device Configuration](#vrf-instances-device-configuration)
- [Application Traffic Recognition](#application-traffic-recognition)
  - [Applications](#applications)
  - [Application Profiles](#application-profiles)
  - [Field Sets](#field-sets)
  - [Router Application-Traffic-Recognition Device Configuration](#router-application-traffic-recognition-device-configuration)
  - [Router Path-selection](#router-path-selection)
- [STUN](#stun)
  - [STUN Client](#stun-client)
  - [STUN Device Configuration](#stun-device-configuration)

## Management

### Agents

#### Agent KernelFib

##### Environment Variables

| Name | Value |
| ---- | ----- |
| KERNELFIB_PROGRAM_ALL_ECMP | 1 |

#### Agents Device Configuration

```eos
!
agent KernelFib environment KERNELFIB_PROGRAM_ALL_ECMP=1
```

### Management Interfaces

#### Management Interfaces Summary

##### IPv4

| Management Interface | Description | Type | VRF | IP Address | Gateway |
| -------------------- | ----------- | ---- | --- | ---------- | ------- |
| Management1/1 | oob_management | oob | MGMT | 172.28.137.227/16 | 172.28.128.1 |

##### IPv6

| Management Interface | Description | Type | VRF | IPv6 Address | IPv6 Gateway |
| -------------------- | ----------- | ---- | --- | ------------ | ------------ |
| Management1/1 | oob_management | oob | MGMT | - | - |

#### Management Interfaces Device Configuration

```eos
!
interface Management1/1
   description oob_management
   no shutdown
   vrf MGMT
   ip address 172.28.137.227/16
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
| Management1/1 | MGMT |

##### NTP Servers

| Server | VRF | Preferred | Burst | iBurst | Version | Min Poll | Max Poll | Local-interface | Key |
| ------ | --- | --------- | ----- | ------ | ------- | -------- | -------- | --------------- | --- |
| 1.pool.ntp.org | MGMT | True | - | True | - | - | - | - | - |

#### NTP Device Configuration

```eos
!
ntp local-interface vrf MGMT Management1/1
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

### Flow Tracking

#### Flow Tracking Hardware

##### Trackers Summary

| Tracker Name | Record Export On Inactive Timeout | Record Export On Interval | Number of Exporters | Applied On |
| ------------ | --------------------------------- | ------------------------- | ------------------- | ---------- |
| FLOW-TRACKER | 70000 | 300000 | 1 | Dps1 |

##### Exporters Summary

| Tracker Name | Exporter Name | Collector IP/Host | Collector Port | Local Interface |
| ------------ | ------------- | ----------------- | -------------- | --------------- |
| FLOW-TRACKER | CV-TELEMETRY | - | - | Loopback0 |

#### Flow Tracking Device Configuration

```eos
!
flow tracking hardware
   tracker FLOW-TRACKER
      record export on inactive timeout 70000
      record export on interval 300000
      exporter CV-TELEMETRY
         collector 127.0.0.1
         local interface Loopback0
         template interval 3600000
   no shutdown
```

## Spanning Tree

### Spanning Tree Summary

STP mode: **none**

### Spanning Tree Device Configuration

```eos
!
spanning-tree mode none
```

## IP Security

### IKE policies

| Policy name | IKE lifetime | Encryption | DH group | Local ID |
| ----------- | ------------ | ---------- | -------- | -------- |
| DP-IKE-POLICY | - | - | - | 10.1.0.66 |
| CP-IKE-POLICY | - | - | - | 10.1.0.66 |

### Security Association policies

| Policy name | ESP Integrity | ESP Encryption | Lifetime | PFS DH Group |
| ----------- | ------------- | -------------- | -------- | ------------ |
| DP-SA-POLICY | - | aes256gcm128 | - | 14 |
| CP-SA-POLICY | - | aes256gcm128 | - | 14 |

### IPSec profiles

| Profile name | IKE policy | SA policy | Connection | DPD Interval | DPD Time | DPD action | Mode | Flow Parallelization |
| ------------ | ---------- | ----------| ---------- | ------------ | -------- | ---------- | ---- | -------------------- |
| DP-PROFILE | DP-IKE-POLICY | DP-SA-POLICY | start | - | - | - | transport | - |
| CP-PROFILE | CP-IKE-POLICY | CP-SA-POLICY | start | - | - | - | transport | - |

### Key controller

| Profile name |
| ------------ |
| DP-PROFILE |

### IP Security Device Configuration

```eos
!
ip security
   !
   ike policy DP-IKE-POLICY
      local-id 10.1.0.66
   !
   ike policy CP-IKE-POLICY
      local-id 10.1.0.66
   !
   sa policy DP-SA-POLICY
      esp encryption aes256gcm128
      pfs dh-group 14
   !
   sa policy CP-SA-POLICY
      esp encryption aes256gcm128
      pfs dh-group 14
   !
   profile DP-PROFILE
      ike-policy DP-IKE-POLICY
      sa-policy DP-SA-POLICY
      connection start
      shared-key 7 <removed>
      dpd 10 50 clear
      mode transport
   !
   profile CP-PROFILE
      ike-policy CP-IKE-POLICY
      sa-policy CP-SA-POLICY
      connection start
      shared-key 7 <removed>
      dpd 10 50 clear
      mode transport
   !
   key controller
      profile DP-PROFILE
```

## Interfaces

### DPS Interfaces

#### DPS Interfaces Summary

| Interface | IP address | Shutdown | MTU | Flow tracker(s) | TCP MSS Ceiling |
| --------- | ---------- | -------- | --- | --------------- | --------------- |
| Dps1 | 10.1.0.66/32 | - | 9214 | Hardware: FLOW-TRACKER |  |

#### DPS Interfaces Device Configuration

```eos
!
interface Dps1
   description DPS Interface
   mtu 9214
   flow tracker hardware FLOW-TRACKER
   ip address 10.1.0.66/32
```

### Ethernet Interfaces

#### Ethernet Interfaces Summary

##### L2

| Interface | Description | Mode | VLANs | Native VLAN | Trunk Group | Channel-Group |
| --------- | ----------- | ---- | ----- | ----------- | ----------- | ------------- |

*Inherited from Port-Channel Interface

##### Encapsulation Dot1q Interfaces

| Interface | Description | Type | Vlan ID | Dot1q VLAN Tag |
| --------- | ----------- | -----| ------- | -------------- |
| Ethernet1/13.2 | P2P_LINK_TO_R1-HUB-LEAF1_Ethernet3.2_vrf_customer2 | l3dot1q | - | 2 |
| Ethernet1/13.3 | P2P_LINK_TO_R1-HUB-LEAF1_Ethernet3.3_vrf_customer3 | l3dot1q | - | 3 |
| Ethernet1/13.4 | P2P_LINK_TO_R1-HUB-LEAF1_Ethernet3.4_vrf_customer4 | l3dot1q | - | 4 |
| Ethernet1/13.5 | P2P_LINK_TO_R1-HUB-LEAF1_Ethernet3.5_vrf_customer5 | l3dot1q | - | 5 |
| Ethernet1/13.6 | P2P_LINK_TO_R1-HUB-LEAF1_Ethernet3.6_vrf_customer6 | l3dot1q | - | 6 |
| Ethernet1/13.7 | P2P_LINK_TO_R1-HUB-LEAF1_Ethernet3.7_vrf_customer7 | l3dot1q | - | 7 |
| Ethernet1/13.8 | P2P_LINK_TO_R1-HUB-LEAF1_Ethernet3.8_vrf_customer8 | l3dot1q | - | 8 |
| Ethernet1/13.9 | P2P_LINK_TO_R1-HUB-LEAF1_Ethernet3.9_vrf_customer9 | l3dot1q | - | 9 |
| Ethernet1/13.10 | P2P_LINK_TO_R1-HUB-LEAF1_Ethernet3.10_vrf_customer10 | l3dot1q | - | 10 |
| Ethernet1/13.11 | P2P_LINK_TO_R1-HUB-LEAF1_Ethernet3.11_vrf_customer11 | l3dot1q | - | 11 |
| Ethernet1/13.12 | P2P_LINK_TO_R1-HUB-LEAF1_Ethernet3.12_vrf_customer12 | l3dot1q | - | 12 |
| Ethernet1/13.13 | P2P_LINK_TO_R1-HUB-LEAF1_Ethernet3.13_vrf_customer13 | l3dot1q | - | 13 |
| Ethernet1/13.14 | P2P_LINK_TO_R1-HUB-LEAF1_Ethernet3.14_vrf_customer14 | l3dot1q | - | 14 |
| Ethernet1/13.15 | P2P_LINK_TO_R1-HUB-LEAF1_Ethernet3.15_vrf_customer15 | l3dot1q | - | 15 |
| Ethernet1/13.16 | P2P_LINK_TO_R1-HUB-LEAF1_Ethernet3.16_vrf_customer16 | l3dot1q | - | 16 |
| Ethernet1/13.17 | P2P_LINK_TO_R1-HUB-LEAF1_Ethernet3.17_vrf_customer17 | l3dot1q | - | 17 |
| Ethernet1/13.18 | P2P_LINK_TO_R1-HUB-LEAF1_Ethernet3.18_vrf_customer18 | l3dot1q | - | 18 |
| Ethernet1/13.19 | P2P_LINK_TO_R1-HUB-LEAF1_Ethernet3.19_vrf_customer19 | l3dot1q | - | 19 |
| Ethernet1/13.20 | P2P_LINK_TO_R1-HUB-LEAF1_Ethernet3.20_vrf_customer20 | l3dot1q | - | 20 |
| Ethernet1/13.21 | P2P_LINK_TO_R1-HUB-LEAF1_Ethernet3.21_vrf_customer21 | l3dot1q | - | 21 |
| Ethernet1/13.22 | P2P_LINK_TO_R1-HUB-LEAF1_Ethernet3.22_vrf_customer22 | l3dot1q | - | 22 |
| Ethernet1/13.23 | P2P_LINK_TO_R1-HUB-LEAF1_Ethernet3.23_vrf_customer23 | l3dot1q | - | 23 |
| Ethernet1/13.24 | P2P_LINK_TO_R1-HUB-LEAF1_Ethernet3.24_vrf_customer24 | l3dot1q | - | 24 |
| Ethernet1/13.25 | P2P_LINK_TO_R1-HUB-LEAF1_Ethernet3.25_vrf_customer25 | l3dot1q | - | 25 |
| Ethernet1/13.26 | P2P_LINK_TO_R1-HUB-LEAF1_Ethernet3.26_vrf_customer26 | l3dot1q | - | 26 |
| Ethernet1/13.27 | P2P_LINK_TO_R1-HUB-LEAF1_Ethernet3.27_vrf_customer27 | l3dot1q | - | 27 |
| Ethernet1/13.28 | P2P_LINK_TO_R1-HUB-LEAF1_Ethernet3.28_vrf_customer28 | l3dot1q | - | 28 |
| Ethernet1/13.29 | P2P_LINK_TO_R1-HUB-LEAF1_Ethernet3.29_vrf_customer29 | l3dot1q | - | 29 |
| Ethernet1/13.30 | P2P_LINK_TO_R1-HUB-LEAF1_Ethernet3.30_vrf_customer30 | l3dot1q | - | 30 |
| Ethernet1/13.31 | P2P_LINK_TO_R1-HUB-LEAF1_Ethernet3.31_vrf_customer31 | l3dot1q | - | 31 |
| Ethernet1/13.32 | P2P_LINK_TO_R1-HUB-LEAF1_Ethernet3.32_vrf_customer32 | l3dot1q | - | 32 |
| Ethernet1/13.33 | P2P_LINK_TO_R1-HUB-LEAF1_Ethernet3.33_vrf_customer33 | l3dot1q | - | 33 |
| Ethernet1/13.34 | P2P_LINK_TO_R1-HUB-LEAF1_Ethernet3.34_vrf_customer34 | l3dot1q | - | 34 |
| Ethernet1/13.35 | P2P_LINK_TO_R1-HUB-LEAF1_Ethernet3.35_vrf_customer35 | l3dot1q | - | 35 |
| Ethernet1/13.36 | P2P_LINK_TO_R1-HUB-LEAF1_Ethernet3.36_vrf_customer36 | l3dot1q | - | 36 |
| Ethernet1/13.37 | P2P_LINK_TO_R1-HUB-LEAF1_Ethernet3.37_vrf_customer37 | l3dot1q | - | 37 |
| Ethernet1/13.38 | P2P_LINK_TO_R1-HUB-LEAF1_Ethernet3.38_vrf_customer38 | l3dot1q | - | 38 |
| Ethernet1/13.39 | P2P_LINK_TO_R1-HUB-LEAF1_Ethernet3.39_vrf_customer39 | l3dot1q | - | 39 |
| Ethernet1/13.40 | P2P_LINK_TO_R1-HUB-LEAF1_Ethernet3.40_vrf_customer40 | l3dot1q | - | 40 |
| Ethernet1/13.41 | P2P_LINK_TO_R1-HUB-LEAF1_Ethernet3.41_vrf_customer41 | l3dot1q | - | 41 |
| Ethernet1/13.42 | P2P_LINK_TO_R1-HUB-LEAF1_Ethernet3.42_vrf_customer42 | l3dot1q | - | 42 |
| Ethernet1/13.43 | P2P_LINK_TO_R1-HUB-LEAF1_Ethernet3.43_vrf_customer43 | l3dot1q | - | 43 |
| Ethernet1/13.44 | P2P_LINK_TO_R1-HUB-LEAF1_Ethernet3.44_vrf_customer44 | l3dot1q | - | 44 |
| Ethernet1/13.45 | P2P_LINK_TO_R1-HUB-LEAF1_Ethernet3.45_vrf_customer45 | l3dot1q | - | 45 |
| Ethernet1/13.46 | P2P_LINK_TO_R1-HUB-LEAF1_Ethernet3.46_vrf_customer46 | l3dot1q | - | 46 |
| Ethernet1/13.47 | P2P_LINK_TO_R1-HUB-LEAF1_Ethernet3.47_vrf_customer47 | l3dot1q | - | 47 |
| Ethernet1/13.48 | P2P_LINK_TO_R1-HUB-LEAF1_Ethernet3.48_vrf_customer48 | l3dot1q | - | 48 |
| Ethernet1/13.49 | P2P_LINK_TO_R1-HUB-LEAF1_Ethernet3.49_vrf_customer49 | l3dot1q | - | 49 |
| Ethernet1/13.50 | P2P_LINK_TO_R1-HUB-LEAF1_Ethernet3.50_vrf_customer50 | l3dot1q | - | 50 |
| Ethernet1/13.51 | P2P_LINK_TO_R1-HUB-LEAF1_Ethernet3.51_vrf_customer51 | l3dot1q | - | 51 |
| Ethernet1/13.52 | P2P_LINK_TO_R1-HUB-LEAF1_Ethernet3.52_vrf_customer52 | l3dot1q | - | 52 |
| Ethernet1/13.53 | P2P_LINK_TO_R1-HUB-LEAF1_Ethernet3.53_vrf_customer53 | l3dot1q | - | 53 |
| Ethernet1/13.54 | P2P_LINK_TO_R1-HUB-LEAF1_Ethernet3.54_vrf_customer54 | l3dot1q | - | 54 |
| Ethernet1/13.55 | P2P_LINK_TO_R1-HUB-LEAF1_Ethernet3.55_vrf_customer55 | l3dot1q | - | 55 |
| Ethernet1/13.56 | P2P_LINK_TO_R1-HUB-LEAF1_Ethernet3.56_vrf_customer56 | l3dot1q | - | 56 |
| Ethernet1/13.57 | P2P_LINK_TO_R1-HUB-LEAF1_Ethernet3.57_vrf_customer57 | l3dot1q | - | 57 |
| Ethernet1/13.58 | P2P_LINK_TO_R1-HUB-LEAF1_Ethernet3.58_vrf_customer58 | l3dot1q | - | 58 |
| Ethernet1/13.59 | P2P_LINK_TO_R1-HUB-LEAF1_Ethernet3.59_vrf_customer59 | l3dot1q | - | 59 |
| Ethernet1/13.60 | P2P_LINK_TO_R1-HUB-LEAF1_Ethernet3.60_vrf_customer60 | l3dot1q | - | 60 |
| Ethernet1/13.61 | P2P_LINK_TO_R1-HUB-LEAF1_Ethernet3.61_vrf_customer61 | l3dot1q | - | 61 |
| Ethernet1/13.62 | P2P_LINK_TO_R1-HUB-LEAF1_Ethernet3.62_vrf_customer62 | l3dot1q | - | 62 |
| Ethernet1/13.63 | P2P_LINK_TO_R1-HUB-LEAF1_Ethernet3.63_vrf_customer63 | l3dot1q | - | 63 |
| Ethernet1/13.64 | P2P_LINK_TO_R1-HUB-LEAF1_Ethernet3.64_vrf_customer64 | l3dot1q | - | 64 |
| Ethernet1/13.254 | P2P_LINK_TO_R1-HUB-LEAF1_Ethernet3.254_vrf_customer1 | l3dot1q | - | 254 |

##### IPv4

| Interface | Description | Type | Channel Group | IP Address | VRF |  MTU | Shutdown | ACL In | ACL Out |
| --------- | ----------- | -----| ------------- | ---------- | ----| ---- | -------- | ------ | ------- |
| Ethernet1/9 | gmpls2_R1-HUB-GMPLS2 | routed | - | 172.16.152.2/30 | default | - | False | - | - |
| Ethernet1/11 | rmpls4_R1-HUB-MPLS4 | routed | - | 172.16.104.2/30 | default | - | False | - | - |
| Ethernet1/13 | P2P_LINK_TO_R1-HUB-LEAF1_Ethernet3 | routed | - | 10.99.0.3/31 | default | 9214 | False | - | - |
| Ethernet1/13.2 | P2P_LINK_TO_R1-HUB-LEAF1_Ethernet3.2_vrf_customer2 | l3dot1q | - | 10.99.0.3/31 | customer2 | 9214 | False | - | - |
| Ethernet1/13.3 | P2P_LINK_TO_R1-HUB-LEAF1_Ethernet3.3_vrf_customer3 | l3dot1q | - | 10.99.0.3/31 | customer3 | 9214 | False | - | - |
| Ethernet1/13.4 | P2P_LINK_TO_R1-HUB-LEAF1_Ethernet3.4_vrf_customer4 | l3dot1q | - | 10.99.0.3/31 | customer4 | 9214 | False | - | - |
| Ethernet1/13.5 | P2P_LINK_TO_R1-HUB-LEAF1_Ethernet3.5_vrf_customer5 | l3dot1q | - | 10.99.0.3/31 | customer5 | 9214 | False | - | - |
| Ethernet1/13.6 | P2P_LINK_TO_R1-HUB-LEAF1_Ethernet3.6_vrf_customer6 | l3dot1q | - | 10.99.0.3/31 | customer6 | 9214 | False | - | - |
| Ethernet1/13.7 | P2P_LINK_TO_R1-HUB-LEAF1_Ethernet3.7_vrf_customer7 | l3dot1q | - | 10.99.0.3/31 | customer7 | 9214 | False | - | - |
| Ethernet1/13.8 | P2P_LINK_TO_R1-HUB-LEAF1_Ethernet3.8_vrf_customer8 | l3dot1q | - | 10.99.0.3/31 | customer8 | 9214 | False | - | - |
| Ethernet1/13.9 | P2P_LINK_TO_R1-HUB-LEAF1_Ethernet3.9_vrf_customer9 | l3dot1q | - | 10.99.0.3/31 | customer9 | 9214 | False | - | - |
| Ethernet1/13.10 | P2P_LINK_TO_R1-HUB-LEAF1_Ethernet3.10_vrf_customer10 | l3dot1q | - | 10.99.0.3/31 | customer10 | 9214 | False | - | - |
| Ethernet1/13.11 | P2P_LINK_TO_R1-HUB-LEAF1_Ethernet3.11_vrf_customer11 | l3dot1q | - | 10.99.0.3/31 | customer11 | 9214 | False | - | - |
| Ethernet1/13.12 | P2P_LINK_TO_R1-HUB-LEAF1_Ethernet3.12_vrf_customer12 | l3dot1q | - | 10.99.0.3/31 | customer12 | 9214 | False | - | - |
| Ethernet1/13.13 | P2P_LINK_TO_R1-HUB-LEAF1_Ethernet3.13_vrf_customer13 | l3dot1q | - | 10.99.0.3/31 | customer13 | 9214 | False | - | - |
| Ethernet1/13.14 | P2P_LINK_TO_R1-HUB-LEAF1_Ethernet3.14_vrf_customer14 | l3dot1q | - | 10.99.0.3/31 | customer14 | 9214 | False | - | - |
| Ethernet1/13.15 | P2P_LINK_TO_R1-HUB-LEAF1_Ethernet3.15_vrf_customer15 | l3dot1q | - | 10.99.0.3/31 | customer15 | 9214 | False | - | - |
| Ethernet1/13.16 | P2P_LINK_TO_R1-HUB-LEAF1_Ethernet3.16_vrf_customer16 | l3dot1q | - | 10.99.0.3/31 | customer16 | 9214 | False | - | - |
| Ethernet1/13.17 | P2P_LINK_TO_R1-HUB-LEAF1_Ethernet3.17_vrf_customer17 | l3dot1q | - | 10.99.0.3/31 | customer17 | 9214 | False | - | - |
| Ethernet1/13.18 | P2P_LINK_TO_R1-HUB-LEAF1_Ethernet3.18_vrf_customer18 | l3dot1q | - | 10.99.0.3/31 | customer18 | 9214 | False | - | - |
| Ethernet1/13.19 | P2P_LINK_TO_R1-HUB-LEAF1_Ethernet3.19_vrf_customer19 | l3dot1q | - | 10.99.0.3/31 | customer19 | 9214 | False | - | - |
| Ethernet1/13.20 | P2P_LINK_TO_R1-HUB-LEAF1_Ethernet3.20_vrf_customer20 | l3dot1q | - | 10.99.0.3/31 | customer20 | 9214 | False | - | - |
| Ethernet1/13.21 | P2P_LINK_TO_R1-HUB-LEAF1_Ethernet3.21_vrf_customer21 | l3dot1q | - | 10.99.0.3/31 | customer21 | 9214 | False | - | - |
| Ethernet1/13.22 | P2P_LINK_TO_R1-HUB-LEAF1_Ethernet3.22_vrf_customer22 | l3dot1q | - | 10.99.0.3/31 | customer22 | 9214 | False | - | - |
| Ethernet1/13.23 | P2P_LINK_TO_R1-HUB-LEAF1_Ethernet3.23_vrf_customer23 | l3dot1q | - | 10.99.0.3/31 | customer23 | 9214 | False | - | - |
| Ethernet1/13.24 | P2P_LINK_TO_R1-HUB-LEAF1_Ethernet3.24_vrf_customer24 | l3dot1q | - | 10.99.0.3/31 | customer24 | 9214 | False | - | - |
| Ethernet1/13.25 | P2P_LINK_TO_R1-HUB-LEAF1_Ethernet3.25_vrf_customer25 | l3dot1q | - | 10.99.0.3/31 | customer25 | 9214 | False | - | - |
| Ethernet1/13.26 | P2P_LINK_TO_R1-HUB-LEAF1_Ethernet3.26_vrf_customer26 | l3dot1q | - | 10.99.0.3/31 | customer26 | 9214 | False | - | - |
| Ethernet1/13.27 | P2P_LINK_TO_R1-HUB-LEAF1_Ethernet3.27_vrf_customer27 | l3dot1q | - | 10.99.0.3/31 | customer27 | 9214 | False | - | - |
| Ethernet1/13.28 | P2P_LINK_TO_R1-HUB-LEAF1_Ethernet3.28_vrf_customer28 | l3dot1q | - | 10.99.0.3/31 | customer28 | 9214 | False | - | - |
| Ethernet1/13.29 | P2P_LINK_TO_R1-HUB-LEAF1_Ethernet3.29_vrf_customer29 | l3dot1q | - | 10.99.0.3/31 | customer29 | 9214 | False | - | - |
| Ethernet1/13.30 | P2P_LINK_TO_R1-HUB-LEAF1_Ethernet3.30_vrf_customer30 | l3dot1q | - | 10.99.0.3/31 | customer30 | 9214 | False | - | - |
| Ethernet1/13.31 | P2P_LINK_TO_R1-HUB-LEAF1_Ethernet3.31_vrf_customer31 | l3dot1q | - | 10.99.0.3/31 | customer31 | 9214 | False | - | - |
| Ethernet1/13.32 | P2P_LINK_TO_R1-HUB-LEAF1_Ethernet3.32_vrf_customer32 | l3dot1q | - | 10.99.0.3/31 | customer32 | 9214 | False | - | - |
| Ethernet1/13.33 | P2P_LINK_TO_R1-HUB-LEAF1_Ethernet3.33_vrf_customer33 | l3dot1q | - | 10.99.0.3/31 | customer33 | 9214 | False | - | - |
| Ethernet1/13.34 | P2P_LINK_TO_R1-HUB-LEAF1_Ethernet3.34_vrf_customer34 | l3dot1q | - | 10.99.0.3/31 | customer34 | 9214 | False | - | - |
| Ethernet1/13.35 | P2P_LINK_TO_R1-HUB-LEAF1_Ethernet3.35_vrf_customer35 | l3dot1q | - | 10.99.0.3/31 | customer35 | 9214 | False | - | - |
| Ethernet1/13.36 | P2P_LINK_TO_R1-HUB-LEAF1_Ethernet3.36_vrf_customer36 | l3dot1q | - | 10.99.0.3/31 | customer36 | 9214 | False | - | - |
| Ethernet1/13.37 | P2P_LINK_TO_R1-HUB-LEAF1_Ethernet3.37_vrf_customer37 | l3dot1q | - | 10.99.0.3/31 | customer37 | 9214 | False | - | - |
| Ethernet1/13.38 | P2P_LINK_TO_R1-HUB-LEAF1_Ethernet3.38_vrf_customer38 | l3dot1q | - | 10.99.0.3/31 | customer38 | 9214 | False | - | - |
| Ethernet1/13.39 | P2P_LINK_TO_R1-HUB-LEAF1_Ethernet3.39_vrf_customer39 | l3dot1q | - | 10.99.0.3/31 | customer39 | 9214 | False | - | - |
| Ethernet1/13.40 | P2P_LINK_TO_R1-HUB-LEAF1_Ethernet3.40_vrf_customer40 | l3dot1q | - | 10.99.0.3/31 | customer40 | 9214 | False | - | - |
| Ethernet1/13.41 | P2P_LINK_TO_R1-HUB-LEAF1_Ethernet3.41_vrf_customer41 | l3dot1q | - | 10.99.0.3/31 | customer41 | 9214 | False | - | - |
| Ethernet1/13.42 | P2P_LINK_TO_R1-HUB-LEAF1_Ethernet3.42_vrf_customer42 | l3dot1q | - | 10.99.0.3/31 | customer42 | 9214 | False | - | - |
| Ethernet1/13.43 | P2P_LINK_TO_R1-HUB-LEAF1_Ethernet3.43_vrf_customer43 | l3dot1q | - | 10.99.0.3/31 | customer43 | 9214 | False | - | - |
| Ethernet1/13.44 | P2P_LINK_TO_R1-HUB-LEAF1_Ethernet3.44_vrf_customer44 | l3dot1q | - | 10.99.0.3/31 | customer44 | 9214 | False | - | - |
| Ethernet1/13.45 | P2P_LINK_TO_R1-HUB-LEAF1_Ethernet3.45_vrf_customer45 | l3dot1q | - | 10.99.0.3/31 | customer45 | 9214 | False | - | - |
| Ethernet1/13.46 | P2P_LINK_TO_R1-HUB-LEAF1_Ethernet3.46_vrf_customer46 | l3dot1q | - | 10.99.0.3/31 | customer46 | 9214 | False | - | - |
| Ethernet1/13.47 | P2P_LINK_TO_R1-HUB-LEAF1_Ethernet3.47_vrf_customer47 | l3dot1q | - | 10.99.0.3/31 | customer47 | 9214 | False | - | - |
| Ethernet1/13.48 | P2P_LINK_TO_R1-HUB-LEAF1_Ethernet3.48_vrf_customer48 | l3dot1q | - | 10.99.0.3/31 | customer48 | 9214 | False | - | - |
| Ethernet1/13.49 | P2P_LINK_TO_R1-HUB-LEAF1_Ethernet3.49_vrf_customer49 | l3dot1q | - | 10.99.0.3/31 | customer49 | 9214 | False | - | - |
| Ethernet1/13.50 | P2P_LINK_TO_R1-HUB-LEAF1_Ethernet3.50_vrf_customer50 | l3dot1q | - | 10.99.0.3/31 | customer50 | 9214 | False | - | - |
| Ethernet1/13.51 | P2P_LINK_TO_R1-HUB-LEAF1_Ethernet3.51_vrf_customer51 | l3dot1q | - | 10.99.0.3/31 | customer51 | 9214 | False | - | - |
| Ethernet1/13.52 | P2P_LINK_TO_R1-HUB-LEAF1_Ethernet3.52_vrf_customer52 | l3dot1q | - | 10.99.0.3/31 | customer52 | 9214 | False | - | - |
| Ethernet1/13.53 | P2P_LINK_TO_R1-HUB-LEAF1_Ethernet3.53_vrf_customer53 | l3dot1q | - | 10.99.0.3/31 | customer53 | 9214 | False | - | - |
| Ethernet1/13.54 | P2P_LINK_TO_R1-HUB-LEAF1_Ethernet3.54_vrf_customer54 | l3dot1q | - | 10.99.0.3/31 | customer54 | 9214 | False | - | - |
| Ethernet1/13.55 | P2P_LINK_TO_R1-HUB-LEAF1_Ethernet3.55_vrf_customer55 | l3dot1q | - | 10.99.0.3/31 | customer55 | 9214 | False | - | - |
| Ethernet1/13.56 | P2P_LINK_TO_R1-HUB-LEAF1_Ethernet3.56_vrf_customer56 | l3dot1q | - | 10.99.0.3/31 | customer56 | 9214 | False | - | - |
| Ethernet1/13.57 | P2P_LINK_TO_R1-HUB-LEAF1_Ethernet3.57_vrf_customer57 | l3dot1q | - | 10.99.0.3/31 | customer57 | 9214 | False | - | - |
| Ethernet1/13.58 | P2P_LINK_TO_R1-HUB-LEAF1_Ethernet3.58_vrf_customer58 | l3dot1q | - | 10.99.0.3/31 | customer58 | 9214 | False | - | - |
| Ethernet1/13.59 | P2P_LINK_TO_R1-HUB-LEAF1_Ethernet3.59_vrf_customer59 | l3dot1q | - | 10.99.0.3/31 | customer59 | 9214 | False | - | - |
| Ethernet1/13.60 | P2P_LINK_TO_R1-HUB-LEAF1_Ethernet3.60_vrf_customer60 | l3dot1q | - | 10.99.0.3/31 | customer60 | 9214 | False | - | - |
| Ethernet1/13.61 | P2P_LINK_TO_R1-HUB-LEAF1_Ethernet3.61_vrf_customer61 | l3dot1q | - | 10.99.0.3/31 | customer61 | 9214 | False | - | - |
| Ethernet1/13.62 | P2P_LINK_TO_R1-HUB-LEAF1_Ethernet3.62_vrf_customer62 | l3dot1q | - | 10.99.0.3/31 | customer62 | 9214 | False | - | - |
| Ethernet1/13.63 | P2P_LINK_TO_R1-HUB-LEAF1_Ethernet3.63_vrf_customer63 | l3dot1q | - | 10.99.0.3/31 | customer63 | 9214 | False | - | - |
| Ethernet1/13.64 | P2P_LINK_TO_R1-HUB-LEAF1_Ethernet3.64_vrf_customer64 | l3dot1q | - | 10.99.0.3/31 | customer64 | 9214 | False | - | - |
| Ethernet1/13.254 | P2P_LINK_TO_R1-HUB-LEAF1_Ethernet3.254_vrf_customer1 | l3dot1q | - | 10.99.0.3/31 | customer1 | 9214 | False | - | - |
| Ethernet1/15 | DIRECT LAN HA LINK | routed | - | 10.10.10.1/31 | default | - | False | - | - |

#### Ethernet Interfaces Device Configuration

```eos
!
interface Ethernet1/9
   description gmpls2_R1-HUB-GMPLS2
   no shutdown
   no switchport
   ip address 172.16.152.2/30
!
interface Ethernet1/11
   description rmpls4_R1-HUB-MPLS4
   no shutdown
   no switchport
   ip address 172.16.104.2/30
!
interface Ethernet1/13
   description P2P_LINK_TO_R1-HUB-LEAF1_Ethernet3
   no shutdown
   mtu 9214
   no switchport
   ip address 10.99.0.3/31
!
interface Ethernet1/13.2
   description P2P_LINK_TO_R1-HUB-LEAF1_Ethernet3.2_vrf_customer2
   no shutdown
   mtu 9214
   encapsulation dot1q vlan 2
   vrf customer2
   ip address 10.99.0.3/31
!
interface Ethernet1/13.3
   description P2P_LINK_TO_R1-HUB-LEAF1_Ethernet3.3_vrf_customer3
   no shutdown
   mtu 9214
   encapsulation dot1q vlan 3
   vrf customer3
   ip address 10.99.0.3/31
!
interface Ethernet1/13.4
   description P2P_LINK_TO_R1-HUB-LEAF1_Ethernet3.4_vrf_customer4
   no shutdown
   mtu 9214
   encapsulation dot1q vlan 4
   vrf customer4
   ip address 10.99.0.3/31
!
interface Ethernet1/13.5
   description P2P_LINK_TO_R1-HUB-LEAF1_Ethernet3.5_vrf_customer5
   no shutdown
   mtu 9214
   encapsulation dot1q vlan 5
   vrf customer5
   ip address 10.99.0.3/31
!
interface Ethernet1/13.6
   description P2P_LINK_TO_R1-HUB-LEAF1_Ethernet3.6_vrf_customer6
   no shutdown
   mtu 9214
   encapsulation dot1q vlan 6
   vrf customer6
   ip address 10.99.0.3/31
!
interface Ethernet1/13.7
   description P2P_LINK_TO_R1-HUB-LEAF1_Ethernet3.7_vrf_customer7
   no shutdown
   mtu 9214
   encapsulation dot1q vlan 7
   vrf customer7
   ip address 10.99.0.3/31
!
interface Ethernet1/13.8
   description P2P_LINK_TO_R1-HUB-LEAF1_Ethernet3.8_vrf_customer8
   no shutdown
   mtu 9214
   encapsulation dot1q vlan 8
   vrf customer8
   ip address 10.99.0.3/31
!
interface Ethernet1/13.9
   description P2P_LINK_TO_R1-HUB-LEAF1_Ethernet3.9_vrf_customer9
   no shutdown
   mtu 9214
   encapsulation dot1q vlan 9
   vrf customer9
   ip address 10.99.0.3/31
!
interface Ethernet1/13.10
   description P2P_LINK_TO_R1-HUB-LEAF1_Ethernet3.10_vrf_customer10
   no shutdown
   mtu 9214
   encapsulation dot1q vlan 10
   vrf customer10
   ip address 10.99.0.3/31
!
interface Ethernet1/13.11
   description P2P_LINK_TO_R1-HUB-LEAF1_Ethernet3.11_vrf_customer11
   no shutdown
   mtu 9214
   encapsulation dot1q vlan 11
   vrf customer11
   ip address 10.99.0.3/31
!
interface Ethernet1/13.12
   description P2P_LINK_TO_R1-HUB-LEAF1_Ethernet3.12_vrf_customer12
   no shutdown
   mtu 9214
   encapsulation dot1q vlan 12
   vrf customer12
   ip address 10.99.0.3/31
!
interface Ethernet1/13.13
   description P2P_LINK_TO_R1-HUB-LEAF1_Ethernet3.13_vrf_customer13
   no shutdown
   mtu 9214
   encapsulation dot1q vlan 13
   vrf customer13
   ip address 10.99.0.3/31
!
interface Ethernet1/13.14
   description P2P_LINK_TO_R1-HUB-LEAF1_Ethernet3.14_vrf_customer14
   no shutdown
   mtu 9214
   encapsulation dot1q vlan 14
   vrf customer14
   ip address 10.99.0.3/31
!
interface Ethernet1/13.15
   description P2P_LINK_TO_R1-HUB-LEAF1_Ethernet3.15_vrf_customer15
   no shutdown
   mtu 9214
   encapsulation dot1q vlan 15
   vrf customer15
   ip address 10.99.0.3/31
!
interface Ethernet1/13.16
   description P2P_LINK_TO_R1-HUB-LEAF1_Ethernet3.16_vrf_customer16
   no shutdown
   mtu 9214
   encapsulation dot1q vlan 16
   vrf customer16
   ip address 10.99.0.3/31
!
interface Ethernet1/13.17
   description P2P_LINK_TO_R1-HUB-LEAF1_Ethernet3.17_vrf_customer17
   no shutdown
   mtu 9214
   encapsulation dot1q vlan 17
   vrf customer17
   ip address 10.99.0.3/31
!
interface Ethernet1/13.18
   description P2P_LINK_TO_R1-HUB-LEAF1_Ethernet3.18_vrf_customer18
   no shutdown
   mtu 9214
   encapsulation dot1q vlan 18
   vrf customer18
   ip address 10.99.0.3/31
!
interface Ethernet1/13.19
   description P2P_LINK_TO_R1-HUB-LEAF1_Ethernet3.19_vrf_customer19
   no shutdown
   mtu 9214
   encapsulation dot1q vlan 19
   vrf customer19
   ip address 10.99.0.3/31
!
interface Ethernet1/13.20
   description P2P_LINK_TO_R1-HUB-LEAF1_Ethernet3.20_vrf_customer20
   no shutdown
   mtu 9214
   encapsulation dot1q vlan 20
   vrf customer20
   ip address 10.99.0.3/31
!
interface Ethernet1/13.21
   description P2P_LINK_TO_R1-HUB-LEAF1_Ethernet3.21_vrf_customer21
   no shutdown
   mtu 9214
   encapsulation dot1q vlan 21
   vrf customer21
   ip address 10.99.0.3/31
!
interface Ethernet1/13.22
   description P2P_LINK_TO_R1-HUB-LEAF1_Ethernet3.22_vrf_customer22
   no shutdown
   mtu 9214
   encapsulation dot1q vlan 22
   vrf customer22
   ip address 10.99.0.3/31
!
interface Ethernet1/13.23
   description P2P_LINK_TO_R1-HUB-LEAF1_Ethernet3.23_vrf_customer23
   no shutdown
   mtu 9214
   encapsulation dot1q vlan 23
   vrf customer23
   ip address 10.99.0.3/31
!
interface Ethernet1/13.24
   description P2P_LINK_TO_R1-HUB-LEAF1_Ethernet3.24_vrf_customer24
   no shutdown
   mtu 9214
   encapsulation dot1q vlan 24
   vrf customer24
   ip address 10.99.0.3/31
!
interface Ethernet1/13.25
   description P2P_LINK_TO_R1-HUB-LEAF1_Ethernet3.25_vrf_customer25
   no shutdown
   mtu 9214
   encapsulation dot1q vlan 25
   vrf customer25
   ip address 10.99.0.3/31
!
interface Ethernet1/13.26
   description P2P_LINK_TO_R1-HUB-LEAF1_Ethernet3.26_vrf_customer26
   no shutdown
   mtu 9214
   encapsulation dot1q vlan 26
   vrf customer26
   ip address 10.99.0.3/31
!
interface Ethernet1/13.27
   description P2P_LINK_TO_R1-HUB-LEAF1_Ethernet3.27_vrf_customer27
   no shutdown
   mtu 9214
   encapsulation dot1q vlan 27
   vrf customer27
   ip address 10.99.0.3/31
!
interface Ethernet1/13.28
   description P2P_LINK_TO_R1-HUB-LEAF1_Ethernet3.28_vrf_customer28
   no shutdown
   mtu 9214
   encapsulation dot1q vlan 28
   vrf customer28
   ip address 10.99.0.3/31
!
interface Ethernet1/13.29
   description P2P_LINK_TO_R1-HUB-LEAF1_Ethernet3.29_vrf_customer29
   no shutdown
   mtu 9214
   encapsulation dot1q vlan 29
   vrf customer29
   ip address 10.99.0.3/31
!
interface Ethernet1/13.30
   description P2P_LINK_TO_R1-HUB-LEAF1_Ethernet3.30_vrf_customer30
   no shutdown
   mtu 9214
   encapsulation dot1q vlan 30
   vrf customer30
   ip address 10.99.0.3/31
!
interface Ethernet1/13.31
   description P2P_LINK_TO_R1-HUB-LEAF1_Ethernet3.31_vrf_customer31
   no shutdown
   mtu 9214
   encapsulation dot1q vlan 31
   vrf customer31
   ip address 10.99.0.3/31
!
interface Ethernet1/13.32
   description P2P_LINK_TO_R1-HUB-LEAF1_Ethernet3.32_vrf_customer32
   no shutdown
   mtu 9214
   encapsulation dot1q vlan 32
   vrf customer32
   ip address 10.99.0.3/31
!
interface Ethernet1/13.33
   description P2P_LINK_TO_R1-HUB-LEAF1_Ethernet3.33_vrf_customer33
   no shutdown
   mtu 9214
   encapsulation dot1q vlan 33
   vrf customer33
   ip address 10.99.0.3/31
!
interface Ethernet1/13.34
   description P2P_LINK_TO_R1-HUB-LEAF1_Ethernet3.34_vrf_customer34
   no shutdown
   mtu 9214
   encapsulation dot1q vlan 34
   vrf customer34
   ip address 10.99.0.3/31
!
interface Ethernet1/13.35
   description P2P_LINK_TO_R1-HUB-LEAF1_Ethernet3.35_vrf_customer35
   no shutdown
   mtu 9214
   encapsulation dot1q vlan 35
   vrf customer35
   ip address 10.99.0.3/31
!
interface Ethernet1/13.36
   description P2P_LINK_TO_R1-HUB-LEAF1_Ethernet3.36_vrf_customer36
   no shutdown
   mtu 9214
   encapsulation dot1q vlan 36
   vrf customer36
   ip address 10.99.0.3/31
!
interface Ethernet1/13.37
   description P2P_LINK_TO_R1-HUB-LEAF1_Ethernet3.37_vrf_customer37
   no shutdown
   mtu 9214
   encapsulation dot1q vlan 37
   vrf customer37
   ip address 10.99.0.3/31
!
interface Ethernet1/13.38
   description P2P_LINK_TO_R1-HUB-LEAF1_Ethernet3.38_vrf_customer38
   no shutdown
   mtu 9214
   encapsulation dot1q vlan 38
   vrf customer38
   ip address 10.99.0.3/31
!
interface Ethernet1/13.39
   description P2P_LINK_TO_R1-HUB-LEAF1_Ethernet3.39_vrf_customer39
   no shutdown
   mtu 9214
   encapsulation dot1q vlan 39
   vrf customer39
   ip address 10.99.0.3/31
!
interface Ethernet1/13.40
   description P2P_LINK_TO_R1-HUB-LEAF1_Ethernet3.40_vrf_customer40
   no shutdown
   mtu 9214
   encapsulation dot1q vlan 40
   vrf customer40
   ip address 10.99.0.3/31
!
interface Ethernet1/13.41
   description P2P_LINK_TO_R1-HUB-LEAF1_Ethernet3.41_vrf_customer41
   no shutdown
   mtu 9214
   encapsulation dot1q vlan 41
   vrf customer41
   ip address 10.99.0.3/31
!
interface Ethernet1/13.42
   description P2P_LINK_TO_R1-HUB-LEAF1_Ethernet3.42_vrf_customer42
   no shutdown
   mtu 9214
   encapsulation dot1q vlan 42
   vrf customer42
   ip address 10.99.0.3/31
!
interface Ethernet1/13.43
   description P2P_LINK_TO_R1-HUB-LEAF1_Ethernet3.43_vrf_customer43
   no shutdown
   mtu 9214
   encapsulation dot1q vlan 43
   vrf customer43
   ip address 10.99.0.3/31
!
interface Ethernet1/13.44
   description P2P_LINK_TO_R1-HUB-LEAF1_Ethernet3.44_vrf_customer44
   no shutdown
   mtu 9214
   encapsulation dot1q vlan 44
   vrf customer44
   ip address 10.99.0.3/31
!
interface Ethernet1/13.45
   description P2P_LINK_TO_R1-HUB-LEAF1_Ethernet3.45_vrf_customer45
   no shutdown
   mtu 9214
   encapsulation dot1q vlan 45
   vrf customer45
   ip address 10.99.0.3/31
!
interface Ethernet1/13.46
   description P2P_LINK_TO_R1-HUB-LEAF1_Ethernet3.46_vrf_customer46
   no shutdown
   mtu 9214
   encapsulation dot1q vlan 46
   vrf customer46
   ip address 10.99.0.3/31
!
interface Ethernet1/13.47
   description P2P_LINK_TO_R1-HUB-LEAF1_Ethernet3.47_vrf_customer47
   no shutdown
   mtu 9214
   encapsulation dot1q vlan 47
   vrf customer47
   ip address 10.99.0.3/31
!
interface Ethernet1/13.48
   description P2P_LINK_TO_R1-HUB-LEAF1_Ethernet3.48_vrf_customer48
   no shutdown
   mtu 9214
   encapsulation dot1q vlan 48
   vrf customer48
   ip address 10.99.0.3/31
!
interface Ethernet1/13.49
   description P2P_LINK_TO_R1-HUB-LEAF1_Ethernet3.49_vrf_customer49
   no shutdown
   mtu 9214
   encapsulation dot1q vlan 49
   vrf customer49
   ip address 10.99.0.3/31
!
interface Ethernet1/13.50
   description P2P_LINK_TO_R1-HUB-LEAF1_Ethernet3.50_vrf_customer50
   no shutdown
   mtu 9214
   encapsulation dot1q vlan 50
   vrf customer50
   ip address 10.99.0.3/31
!
interface Ethernet1/13.51
   description P2P_LINK_TO_R1-HUB-LEAF1_Ethernet3.51_vrf_customer51
   no shutdown
   mtu 9214
   encapsulation dot1q vlan 51
   vrf customer51
   ip address 10.99.0.3/31
!
interface Ethernet1/13.52
   description P2P_LINK_TO_R1-HUB-LEAF1_Ethernet3.52_vrf_customer52
   no shutdown
   mtu 9214
   encapsulation dot1q vlan 52
   vrf customer52
   ip address 10.99.0.3/31
!
interface Ethernet1/13.53
   description P2P_LINK_TO_R1-HUB-LEAF1_Ethernet3.53_vrf_customer53
   no shutdown
   mtu 9214
   encapsulation dot1q vlan 53
   vrf customer53
   ip address 10.99.0.3/31
!
interface Ethernet1/13.54
   description P2P_LINK_TO_R1-HUB-LEAF1_Ethernet3.54_vrf_customer54
   no shutdown
   mtu 9214
   encapsulation dot1q vlan 54
   vrf customer54
   ip address 10.99.0.3/31
!
interface Ethernet1/13.55
   description P2P_LINK_TO_R1-HUB-LEAF1_Ethernet3.55_vrf_customer55
   no shutdown
   mtu 9214
   encapsulation dot1q vlan 55
   vrf customer55
   ip address 10.99.0.3/31
!
interface Ethernet1/13.56
   description P2P_LINK_TO_R1-HUB-LEAF1_Ethernet3.56_vrf_customer56
   no shutdown
   mtu 9214
   encapsulation dot1q vlan 56
   vrf customer56
   ip address 10.99.0.3/31
!
interface Ethernet1/13.57
   description P2P_LINK_TO_R1-HUB-LEAF1_Ethernet3.57_vrf_customer57
   no shutdown
   mtu 9214
   encapsulation dot1q vlan 57
   vrf customer57
   ip address 10.99.0.3/31
!
interface Ethernet1/13.58
   description P2P_LINK_TO_R1-HUB-LEAF1_Ethernet3.58_vrf_customer58
   no shutdown
   mtu 9214
   encapsulation dot1q vlan 58
   vrf customer58
   ip address 10.99.0.3/31
!
interface Ethernet1/13.59
   description P2P_LINK_TO_R1-HUB-LEAF1_Ethernet3.59_vrf_customer59
   no shutdown
   mtu 9214
   encapsulation dot1q vlan 59
   vrf customer59
   ip address 10.99.0.3/31
!
interface Ethernet1/13.60
   description P2P_LINK_TO_R1-HUB-LEAF1_Ethernet3.60_vrf_customer60
   no shutdown
   mtu 9214
   encapsulation dot1q vlan 60
   vrf customer60
   ip address 10.99.0.3/31
!
interface Ethernet1/13.61
   description P2P_LINK_TO_R1-HUB-LEAF1_Ethernet3.61_vrf_customer61
   no shutdown
   mtu 9214
   encapsulation dot1q vlan 61
   vrf customer61
   ip address 10.99.0.3/31
!
interface Ethernet1/13.62
   description P2P_LINK_TO_R1-HUB-LEAF1_Ethernet3.62_vrf_customer62
   no shutdown
   mtu 9214
   encapsulation dot1q vlan 62
   vrf customer62
   ip address 10.99.0.3/31
!
interface Ethernet1/13.63
   description P2P_LINK_TO_R1-HUB-LEAF1_Ethernet3.63_vrf_customer63
   no shutdown
   mtu 9214
   encapsulation dot1q vlan 63
   vrf customer63
   ip address 10.99.0.3/31
!
interface Ethernet1/13.64
   description P2P_LINK_TO_R1-HUB-LEAF1_Ethernet3.64_vrf_customer64
   no shutdown
   mtu 9214
   encapsulation dot1q vlan 64
   vrf customer64
   ip address 10.99.0.3/31
!
interface Ethernet1/13.254
   description P2P_LINK_TO_R1-HUB-LEAF1_Ethernet3.254_vrf_customer1
   no shutdown
   mtu 9214
   encapsulation dot1q vlan 254
   vrf customer1
   ip address 10.99.0.3/31
!
interface Ethernet1/15
   description DIRECT LAN HA LINK
   no shutdown
   no switchport
   ip address 10.10.10.1/31
```

### Loopback Interfaces

#### Loopback Interfaces Summary

##### IPv4

| Interface | Description | VRF | IP Address |
| --------- | ----------- | --- | ---------- |
| Loopback0 | Router_ID | default | 10.1.0.2/32 |

##### IPv6

| Interface | Description | VRF | IPv6 Address |
| --------- | ----------- | --- | ------------ |
| Loopback0 | Router_ID | default | - |

#### Loopback Interfaces Device Configuration

```eos
!
interface Loopback0
   description Router_ID
   no shutdown
   ip address 10.1.0.2/32
```

### VXLAN Interface

#### VXLAN Interface Summary

| Setting | Value |
| ------- | ----- |
| Source Interface | Dps1 |
| UDP port | 4789 |

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
| default | 1 | - |

#### VXLAN Interface Device Configuration

```eos
!
interface Vxlan1
   description r1-hub-wan2_VTEP
   vxlan source-interface Dps1
   vxlan udp-port 4789
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
   vxlan vrf default vni 1
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
| default | 172.16.51.4/30 | 172.16.152.1 | - | 1 | - | - | - |
| default | 172.16.52.4/30 | 172.16.152.1 | - | 1 | - | - | - |
| default | 172.16.152.0/30 | 172.16.152.1 | - | 1 | - | - | - |
| default | 172.16.202.0/30 | 172.16.152.1 | - | 1 | - | - | - |
| default | 172.16.51.20/30 | 172.16.104.1 | - | 1 | - | - | - |
| default | 172.16.52.20/30 | 172.16.104.1 | - | 1 | - | - | - |
| default | 172.16.104.0/30 | 172.16.104.1 | - | 1 | - | - | - |
| default | 172.16.114.0/30 | 172.16.104.1 | - | 1 | - | - | - |
| default | 172.16.124.0/30 | 172.16.104.1 | - | 1 | - | - | - |

#### Static Routes Device Configuration

```eos
!
ip route vrf MGMT 0.0.0.0/0 172.28.128.1
ip route 172.16.51.4/30 172.16.152.1
ip route 172.16.52.4/30 172.16.152.1
ip route 172.16.152.0/30 172.16.152.1
ip route 172.16.202.0/30 172.16.152.1
ip route 172.16.51.20/30 172.16.104.1
ip route 172.16.52.20/30 172.16.104.1
ip route 172.16.104.0/30 172.16.104.1
ip route 172.16.114.0/30 172.16.104.1
ip route 172.16.124.0/30 172.16.104.1
```

### Router Adaptive Virtual Topology

#### Router Adaptive Virtual Topology Summary

Topology role: transit region

| Hierarchy | Name | ID |
| --------- | ---- | -- |
| Region | Region1 | 1 |
| Zone | Region1-ZONE | 1 |
| Site | REGION1_HUB | 111 |

#### AVT Profiles

| Profile name | Load balance policy | Internet exit policy |
| ------------ | ------------------- | -------------------- |
| DEFAULT-AVT-POLICY-CONTROL-PLANE | LB-DEFAULT-AVT-POLICY-CONTROL-PLANE | - |
| DEFAULT-AVT-POLICY-DEFAULT | LB-DEFAULT-AVT-POLICY-DEFAULT | - |
| TELEPRES-AVT-POLICY-DEFAULT | LB-TELEPRES-AVT-POLICY-DEFAULT | - |
| TELEPRES-AVT-POLICY-VOICE | LB-TELEPRES-AVT-POLICY-VOICE | - |

#### AVT Policies

##### AVT policy DEFAULT-AVT-POLICY-WITH-CP

| Application profile | AVT Profile | Traffic Class | DSCP |
| ------------------- | ----------- | ------------- | ---- |
| APP-PROFILE-CONTROL-PLANE | DEFAULT-AVT-POLICY-CONTROL-PLANE | - | - |
| default | DEFAULT-AVT-POLICY-DEFAULT | - | - |

##### AVT policy TELEPRES-AVT-POLICY

| Application profile | AVT Profile | Traffic Class | DSCP |
| ------------------- | ----------- | ------------- | ---- |
| VOICE | TELEPRES-AVT-POLICY-VOICE | - | - |
| default | TELEPRES-AVT-POLICY-DEFAULT | - | - |

#### VRFs configuration

##### VRF customer1

| AVT policy |
| ---------- |
| TELEPRES-AVT-POLICY |

| AVT Profile | AVT ID |
| ----------- | ------ |
| TELEPRES-AVT-POLICY-DEFAULT | 1 |
| TELEPRES-AVT-POLICY-VOICE | 2 |

##### VRF customer2

| AVT policy |
| ---------- |
| TELEPRES-AVT-POLICY |

| AVT Profile | AVT ID |
| ----------- | ------ |
| TELEPRES-AVT-POLICY-DEFAULT | 1 |
| TELEPRES-AVT-POLICY-VOICE | 2 |

##### VRF customer3

| AVT policy |
| ---------- |
| TELEPRES-AVT-POLICY |

| AVT Profile | AVT ID |
| ----------- | ------ |
| TELEPRES-AVT-POLICY-DEFAULT | 1 |
| TELEPRES-AVT-POLICY-VOICE | 2 |

##### VRF customer4

| AVT policy |
| ---------- |
| TELEPRES-AVT-POLICY |

| AVT Profile | AVT ID |
| ----------- | ------ |
| TELEPRES-AVT-POLICY-DEFAULT | 1 |
| TELEPRES-AVT-POLICY-VOICE | 2 |

##### VRF customer5

| AVT policy |
| ---------- |
| TELEPRES-AVT-POLICY |

| AVT Profile | AVT ID |
| ----------- | ------ |
| TELEPRES-AVT-POLICY-DEFAULT | 1 |
| TELEPRES-AVT-POLICY-VOICE | 2 |

##### VRF customer6

| AVT policy |
| ---------- |
| TELEPRES-AVT-POLICY |

| AVT Profile | AVT ID |
| ----------- | ------ |
| TELEPRES-AVT-POLICY-DEFAULT | 1 |
| TELEPRES-AVT-POLICY-VOICE | 2 |

##### VRF customer7

| AVT policy |
| ---------- |
| TELEPRES-AVT-POLICY |

| AVT Profile | AVT ID |
| ----------- | ------ |
| TELEPRES-AVT-POLICY-DEFAULT | 1 |
| TELEPRES-AVT-POLICY-VOICE | 2 |

##### VRF customer8

| AVT policy |
| ---------- |
| TELEPRES-AVT-POLICY |

| AVT Profile | AVT ID |
| ----------- | ------ |
| TELEPRES-AVT-POLICY-DEFAULT | 1 |
| TELEPRES-AVT-POLICY-VOICE | 2 |

##### VRF customer9

| AVT policy |
| ---------- |
| TELEPRES-AVT-POLICY |

| AVT Profile | AVT ID |
| ----------- | ------ |
| TELEPRES-AVT-POLICY-DEFAULT | 1 |
| TELEPRES-AVT-POLICY-VOICE | 2 |

##### VRF customer10

| AVT policy |
| ---------- |
| TELEPRES-AVT-POLICY |

| AVT Profile | AVT ID |
| ----------- | ------ |
| TELEPRES-AVT-POLICY-DEFAULT | 1 |
| TELEPRES-AVT-POLICY-VOICE | 2 |

##### VRF customer11

| AVT policy |
| ---------- |
| TELEPRES-AVT-POLICY |

| AVT Profile | AVT ID |
| ----------- | ------ |
| TELEPRES-AVT-POLICY-DEFAULT | 1 |
| TELEPRES-AVT-POLICY-VOICE | 2 |

##### VRF customer12

| AVT policy |
| ---------- |
| TELEPRES-AVT-POLICY |

| AVT Profile | AVT ID |
| ----------- | ------ |
| TELEPRES-AVT-POLICY-DEFAULT | 1 |
| TELEPRES-AVT-POLICY-VOICE | 2 |

##### VRF customer13

| AVT policy |
| ---------- |
| TELEPRES-AVT-POLICY |

| AVT Profile | AVT ID |
| ----------- | ------ |
| TELEPRES-AVT-POLICY-DEFAULT | 1 |
| TELEPRES-AVT-POLICY-VOICE | 2 |

##### VRF customer14

| AVT policy |
| ---------- |
| TELEPRES-AVT-POLICY |

| AVT Profile | AVT ID |
| ----------- | ------ |
| TELEPRES-AVT-POLICY-DEFAULT | 1 |
| TELEPRES-AVT-POLICY-VOICE | 2 |

##### VRF customer15

| AVT policy |
| ---------- |
| TELEPRES-AVT-POLICY |

| AVT Profile | AVT ID |
| ----------- | ------ |
| TELEPRES-AVT-POLICY-DEFAULT | 1 |
| TELEPRES-AVT-POLICY-VOICE | 2 |

##### VRF customer16

| AVT policy |
| ---------- |
| TELEPRES-AVT-POLICY |

| AVT Profile | AVT ID |
| ----------- | ------ |
| TELEPRES-AVT-POLICY-DEFAULT | 1 |
| TELEPRES-AVT-POLICY-VOICE | 2 |

##### VRF customer17

| AVT policy |
| ---------- |
| TELEPRES-AVT-POLICY |

| AVT Profile | AVT ID |
| ----------- | ------ |
| TELEPRES-AVT-POLICY-DEFAULT | 1 |
| TELEPRES-AVT-POLICY-VOICE | 2 |

##### VRF customer18

| AVT policy |
| ---------- |
| TELEPRES-AVT-POLICY |

| AVT Profile | AVT ID |
| ----------- | ------ |
| TELEPRES-AVT-POLICY-DEFAULT | 1 |
| TELEPRES-AVT-POLICY-VOICE | 2 |

##### VRF customer19

| AVT policy |
| ---------- |
| TELEPRES-AVT-POLICY |

| AVT Profile | AVT ID |
| ----------- | ------ |
| TELEPRES-AVT-POLICY-DEFAULT | 1 |
| TELEPRES-AVT-POLICY-VOICE | 2 |

##### VRF customer20

| AVT policy |
| ---------- |
| TELEPRES-AVT-POLICY |

| AVT Profile | AVT ID |
| ----------- | ------ |
| TELEPRES-AVT-POLICY-DEFAULT | 1 |
| TELEPRES-AVT-POLICY-VOICE | 2 |

##### VRF customer21

| AVT policy |
| ---------- |
| TELEPRES-AVT-POLICY |

| AVT Profile | AVT ID |
| ----------- | ------ |
| TELEPRES-AVT-POLICY-DEFAULT | 1 |
| TELEPRES-AVT-POLICY-VOICE | 2 |

##### VRF customer22

| AVT policy |
| ---------- |
| TELEPRES-AVT-POLICY |

| AVT Profile | AVT ID |
| ----------- | ------ |
| TELEPRES-AVT-POLICY-DEFAULT | 1 |
| TELEPRES-AVT-POLICY-VOICE | 2 |

##### VRF customer23

| AVT policy |
| ---------- |
| TELEPRES-AVT-POLICY |

| AVT Profile | AVT ID |
| ----------- | ------ |
| TELEPRES-AVT-POLICY-DEFAULT | 1 |
| TELEPRES-AVT-POLICY-VOICE | 2 |

##### VRF customer24

| AVT policy |
| ---------- |
| TELEPRES-AVT-POLICY |

| AVT Profile | AVT ID |
| ----------- | ------ |
| TELEPRES-AVT-POLICY-DEFAULT | 1 |
| TELEPRES-AVT-POLICY-VOICE | 2 |

##### VRF customer25

| AVT policy |
| ---------- |
| TELEPRES-AVT-POLICY |

| AVT Profile | AVT ID |
| ----------- | ------ |
| TELEPRES-AVT-POLICY-DEFAULT | 1 |
| TELEPRES-AVT-POLICY-VOICE | 2 |

##### VRF customer26

| AVT policy |
| ---------- |
| TELEPRES-AVT-POLICY |

| AVT Profile | AVT ID |
| ----------- | ------ |
| TELEPRES-AVT-POLICY-DEFAULT | 1 |
| TELEPRES-AVT-POLICY-VOICE | 2 |

##### VRF customer27

| AVT policy |
| ---------- |
| TELEPRES-AVT-POLICY |

| AVT Profile | AVT ID |
| ----------- | ------ |
| TELEPRES-AVT-POLICY-DEFAULT | 1 |
| TELEPRES-AVT-POLICY-VOICE | 2 |

##### VRF customer28

| AVT policy |
| ---------- |
| TELEPRES-AVT-POLICY |

| AVT Profile | AVT ID |
| ----------- | ------ |
| TELEPRES-AVT-POLICY-DEFAULT | 1 |
| TELEPRES-AVT-POLICY-VOICE | 2 |

##### VRF customer29

| AVT policy |
| ---------- |
| TELEPRES-AVT-POLICY |

| AVT Profile | AVT ID |
| ----------- | ------ |
| TELEPRES-AVT-POLICY-DEFAULT | 1 |
| TELEPRES-AVT-POLICY-VOICE | 2 |

##### VRF customer30

| AVT policy |
| ---------- |
| TELEPRES-AVT-POLICY |

| AVT Profile | AVT ID |
| ----------- | ------ |
| TELEPRES-AVT-POLICY-DEFAULT | 1 |
| TELEPRES-AVT-POLICY-VOICE | 2 |

##### VRF customer31

| AVT policy |
| ---------- |
| TELEPRES-AVT-POLICY |

| AVT Profile | AVT ID |
| ----------- | ------ |
| TELEPRES-AVT-POLICY-DEFAULT | 1 |
| TELEPRES-AVT-POLICY-VOICE | 2 |

##### VRF customer32

| AVT policy |
| ---------- |
| TELEPRES-AVT-POLICY |

| AVT Profile | AVT ID |
| ----------- | ------ |
| TELEPRES-AVT-POLICY-DEFAULT | 1 |
| TELEPRES-AVT-POLICY-VOICE | 2 |

##### VRF customer33

| AVT policy |
| ---------- |
| TELEPRES-AVT-POLICY |

| AVT Profile | AVT ID |
| ----------- | ------ |
| TELEPRES-AVT-POLICY-DEFAULT | 1 |
| TELEPRES-AVT-POLICY-VOICE | 2 |

##### VRF customer34

| AVT policy |
| ---------- |
| TELEPRES-AVT-POLICY |

| AVT Profile | AVT ID |
| ----------- | ------ |
| TELEPRES-AVT-POLICY-DEFAULT | 1 |
| TELEPRES-AVT-POLICY-VOICE | 2 |

##### VRF customer35

| AVT policy |
| ---------- |
| TELEPRES-AVT-POLICY |

| AVT Profile | AVT ID |
| ----------- | ------ |
| TELEPRES-AVT-POLICY-DEFAULT | 1 |
| TELEPRES-AVT-POLICY-VOICE | 2 |

##### VRF customer36

| AVT policy |
| ---------- |
| TELEPRES-AVT-POLICY |

| AVT Profile | AVT ID |
| ----------- | ------ |
| TELEPRES-AVT-POLICY-DEFAULT | 1 |
| TELEPRES-AVT-POLICY-VOICE | 2 |

##### VRF customer37

| AVT policy |
| ---------- |
| TELEPRES-AVT-POLICY |

| AVT Profile | AVT ID |
| ----------- | ------ |
| TELEPRES-AVT-POLICY-DEFAULT | 1 |
| TELEPRES-AVT-POLICY-VOICE | 2 |

##### VRF customer38

| AVT policy |
| ---------- |
| TELEPRES-AVT-POLICY |

| AVT Profile | AVT ID |
| ----------- | ------ |
| TELEPRES-AVT-POLICY-DEFAULT | 1 |
| TELEPRES-AVT-POLICY-VOICE | 2 |

##### VRF customer39

| AVT policy |
| ---------- |
| TELEPRES-AVT-POLICY |

| AVT Profile | AVT ID |
| ----------- | ------ |
| TELEPRES-AVT-POLICY-DEFAULT | 1 |
| TELEPRES-AVT-POLICY-VOICE | 2 |

##### VRF customer40

| AVT policy |
| ---------- |
| TELEPRES-AVT-POLICY |

| AVT Profile | AVT ID |
| ----------- | ------ |
| TELEPRES-AVT-POLICY-DEFAULT | 1 |
| TELEPRES-AVT-POLICY-VOICE | 2 |

##### VRF customer41

| AVT policy |
| ---------- |
| TELEPRES-AVT-POLICY |

| AVT Profile | AVT ID |
| ----------- | ------ |
| TELEPRES-AVT-POLICY-DEFAULT | 1 |
| TELEPRES-AVT-POLICY-VOICE | 2 |

##### VRF customer42

| AVT policy |
| ---------- |
| TELEPRES-AVT-POLICY |

| AVT Profile | AVT ID |
| ----------- | ------ |
| TELEPRES-AVT-POLICY-DEFAULT | 1 |
| TELEPRES-AVT-POLICY-VOICE | 2 |

##### VRF customer43

| AVT policy |
| ---------- |
| TELEPRES-AVT-POLICY |

| AVT Profile | AVT ID |
| ----------- | ------ |
| TELEPRES-AVT-POLICY-DEFAULT | 1 |
| TELEPRES-AVT-POLICY-VOICE | 2 |

##### VRF customer44

| AVT policy |
| ---------- |
| TELEPRES-AVT-POLICY |

| AVT Profile | AVT ID |
| ----------- | ------ |
| TELEPRES-AVT-POLICY-DEFAULT | 1 |
| TELEPRES-AVT-POLICY-VOICE | 2 |

##### VRF customer45

| AVT policy |
| ---------- |
| TELEPRES-AVT-POLICY |

| AVT Profile | AVT ID |
| ----------- | ------ |
| TELEPRES-AVT-POLICY-DEFAULT | 1 |
| TELEPRES-AVT-POLICY-VOICE | 2 |

##### VRF customer46

| AVT policy |
| ---------- |
| TELEPRES-AVT-POLICY |

| AVT Profile | AVT ID |
| ----------- | ------ |
| TELEPRES-AVT-POLICY-DEFAULT | 1 |
| TELEPRES-AVT-POLICY-VOICE | 2 |

##### VRF customer47

| AVT policy |
| ---------- |
| TELEPRES-AVT-POLICY |

| AVT Profile | AVT ID |
| ----------- | ------ |
| TELEPRES-AVT-POLICY-DEFAULT | 1 |
| TELEPRES-AVT-POLICY-VOICE | 2 |

##### VRF customer48

| AVT policy |
| ---------- |
| TELEPRES-AVT-POLICY |

| AVT Profile | AVT ID |
| ----------- | ------ |
| TELEPRES-AVT-POLICY-DEFAULT | 1 |
| TELEPRES-AVT-POLICY-VOICE | 2 |

##### VRF customer49

| AVT policy |
| ---------- |
| TELEPRES-AVT-POLICY |

| AVT Profile | AVT ID |
| ----------- | ------ |
| TELEPRES-AVT-POLICY-DEFAULT | 1 |
| TELEPRES-AVT-POLICY-VOICE | 2 |

##### VRF customer50

| AVT policy |
| ---------- |
| TELEPRES-AVT-POLICY |

| AVT Profile | AVT ID |
| ----------- | ------ |
| TELEPRES-AVT-POLICY-DEFAULT | 1 |
| TELEPRES-AVT-POLICY-VOICE | 2 |

##### VRF customer51

| AVT policy |
| ---------- |
| TELEPRES-AVT-POLICY |

| AVT Profile | AVT ID |
| ----------- | ------ |
| TELEPRES-AVT-POLICY-DEFAULT | 1 |
| TELEPRES-AVT-POLICY-VOICE | 2 |

##### VRF customer52

| AVT policy |
| ---------- |
| TELEPRES-AVT-POLICY |

| AVT Profile | AVT ID |
| ----------- | ------ |
| TELEPRES-AVT-POLICY-DEFAULT | 1 |
| TELEPRES-AVT-POLICY-VOICE | 2 |

##### VRF customer53

| AVT policy |
| ---------- |
| TELEPRES-AVT-POLICY |

| AVT Profile | AVT ID |
| ----------- | ------ |
| TELEPRES-AVT-POLICY-DEFAULT | 1 |
| TELEPRES-AVT-POLICY-VOICE | 2 |

##### VRF customer54

| AVT policy |
| ---------- |
| TELEPRES-AVT-POLICY |

| AVT Profile | AVT ID |
| ----------- | ------ |
| TELEPRES-AVT-POLICY-DEFAULT | 1 |
| TELEPRES-AVT-POLICY-VOICE | 2 |

##### VRF customer55

| AVT policy |
| ---------- |
| TELEPRES-AVT-POLICY |

| AVT Profile | AVT ID |
| ----------- | ------ |
| TELEPRES-AVT-POLICY-DEFAULT | 1 |
| TELEPRES-AVT-POLICY-VOICE | 2 |

##### VRF customer56

| AVT policy |
| ---------- |
| TELEPRES-AVT-POLICY |

| AVT Profile | AVT ID |
| ----------- | ------ |
| TELEPRES-AVT-POLICY-DEFAULT | 1 |
| TELEPRES-AVT-POLICY-VOICE | 2 |

##### VRF customer57

| AVT policy |
| ---------- |
| TELEPRES-AVT-POLICY |

| AVT Profile | AVT ID |
| ----------- | ------ |
| TELEPRES-AVT-POLICY-DEFAULT | 1 |
| TELEPRES-AVT-POLICY-VOICE | 2 |

##### VRF customer58

| AVT policy |
| ---------- |
| TELEPRES-AVT-POLICY |

| AVT Profile | AVT ID |
| ----------- | ------ |
| TELEPRES-AVT-POLICY-DEFAULT | 1 |
| TELEPRES-AVT-POLICY-VOICE | 2 |

##### VRF customer59

| AVT policy |
| ---------- |
| TELEPRES-AVT-POLICY |

| AVT Profile | AVT ID |
| ----------- | ------ |
| TELEPRES-AVT-POLICY-DEFAULT | 1 |
| TELEPRES-AVT-POLICY-VOICE | 2 |

##### VRF customer60

| AVT policy |
| ---------- |
| TELEPRES-AVT-POLICY |

| AVT Profile | AVT ID |
| ----------- | ------ |
| TELEPRES-AVT-POLICY-DEFAULT | 1 |
| TELEPRES-AVT-POLICY-VOICE | 2 |

##### VRF customer61

| AVT policy |
| ---------- |
| TELEPRES-AVT-POLICY |

| AVT Profile | AVT ID |
| ----------- | ------ |
| TELEPRES-AVT-POLICY-DEFAULT | 1 |
| TELEPRES-AVT-POLICY-VOICE | 2 |

##### VRF customer62

| AVT policy |
| ---------- |
| TELEPRES-AVT-POLICY |

| AVT Profile | AVT ID |
| ----------- | ------ |
| TELEPRES-AVT-POLICY-DEFAULT | 1 |
| TELEPRES-AVT-POLICY-VOICE | 2 |

##### VRF customer63

| AVT policy |
| ---------- |
| TELEPRES-AVT-POLICY |

| AVT Profile | AVT ID |
| ----------- | ------ |
| TELEPRES-AVT-POLICY-DEFAULT | 1 |
| TELEPRES-AVT-POLICY-VOICE | 2 |

##### VRF customer64

| AVT policy |
| ---------- |
| TELEPRES-AVT-POLICY |

| AVT Profile | AVT ID |
| ----------- | ------ |
| TELEPRES-AVT-POLICY-DEFAULT | 1 |
| TELEPRES-AVT-POLICY-VOICE | 2 |

##### VRF default

| AVT policy |
| ---------- |
| DEFAULT-AVT-POLICY-WITH-CP |

| AVT Profile | AVT ID |
| ----------- | ------ |
| DEFAULT-AVT-POLICY-DEFAULT | 1 |
| DEFAULT-AVT-POLICY-CONTROL-PLANE | 254 |

#### Router Adaptive Virtual Topology Configuration

```eos
!
router adaptive-virtual-topology
   topology role transit region
   region Region1 id 1
   zone Region1-ZONE id 1
   site REGION1_HUB id 111
   !
   policy DEFAULT-AVT-POLICY-WITH-CP
      !
      match application-profile APP-PROFILE-CONTROL-PLANE
         avt profile DEFAULT-AVT-POLICY-CONTROL-PLANE
      !
      match application-profile default
         avt profile DEFAULT-AVT-POLICY-DEFAULT
   !
   policy TELEPRES-AVT-POLICY
      !
      match application-profile VOICE
         avt profile TELEPRES-AVT-POLICY-VOICE
      !
      match application-profile default
         avt profile TELEPRES-AVT-POLICY-DEFAULT
   !
   profile DEFAULT-AVT-POLICY-CONTROL-PLANE
      path-selection load-balance LB-DEFAULT-AVT-POLICY-CONTROL-PLANE
   !
   profile DEFAULT-AVT-POLICY-DEFAULT
      path-selection load-balance LB-DEFAULT-AVT-POLICY-DEFAULT
   !
   profile TELEPRES-AVT-POLICY-DEFAULT
      path-selection load-balance LB-TELEPRES-AVT-POLICY-DEFAULT
   !
   profile TELEPRES-AVT-POLICY-VOICE
      path-selection load-balance LB-TELEPRES-AVT-POLICY-VOICE
   !
   vrf customer1
      avt policy TELEPRES-AVT-POLICY
      avt profile TELEPRES-AVT-POLICY-DEFAULT id 1
      avt profile TELEPRES-AVT-POLICY-VOICE id 2
   !
   vrf customer2
      avt policy TELEPRES-AVT-POLICY
      avt profile TELEPRES-AVT-POLICY-DEFAULT id 1
      avt profile TELEPRES-AVT-POLICY-VOICE id 2
   !
   vrf customer3
      avt policy TELEPRES-AVT-POLICY
      avt profile TELEPRES-AVT-POLICY-DEFAULT id 1
      avt profile TELEPRES-AVT-POLICY-VOICE id 2
   !
   vrf customer4
      avt policy TELEPRES-AVT-POLICY
      avt profile TELEPRES-AVT-POLICY-DEFAULT id 1
      avt profile TELEPRES-AVT-POLICY-VOICE id 2
   !
   vrf customer5
      avt policy TELEPRES-AVT-POLICY
      avt profile TELEPRES-AVT-POLICY-DEFAULT id 1
      avt profile TELEPRES-AVT-POLICY-VOICE id 2
   !
   vrf customer6
      avt policy TELEPRES-AVT-POLICY
      avt profile TELEPRES-AVT-POLICY-DEFAULT id 1
      avt profile TELEPRES-AVT-POLICY-VOICE id 2
   !
   vrf customer7
      avt policy TELEPRES-AVT-POLICY
      avt profile TELEPRES-AVT-POLICY-DEFAULT id 1
      avt profile TELEPRES-AVT-POLICY-VOICE id 2
   !
   vrf customer8
      avt policy TELEPRES-AVT-POLICY
      avt profile TELEPRES-AVT-POLICY-DEFAULT id 1
      avt profile TELEPRES-AVT-POLICY-VOICE id 2
   !
   vrf customer9
      avt policy TELEPRES-AVT-POLICY
      avt profile TELEPRES-AVT-POLICY-DEFAULT id 1
      avt profile TELEPRES-AVT-POLICY-VOICE id 2
   !
   vrf customer10
      avt policy TELEPRES-AVT-POLICY
      avt profile TELEPRES-AVT-POLICY-DEFAULT id 1
      avt profile TELEPRES-AVT-POLICY-VOICE id 2
   !
   vrf customer11
      avt policy TELEPRES-AVT-POLICY
      avt profile TELEPRES-AVT-POLICY-DEFAULT id 1
      avt profile TELEPRES-AVT-POLICY-VOICE id 2
   !
   vrf customer12
      avt policy TELEPRES-AVT-POLICY
      avt profile TELEPRES-AVT-POLICY-DEFAULT id 1
      avt profile TELEPRES-AVT-POLICY-VOICE id 2
   !
   vrf customer13
      avt policy TELEPRES-AVT-POLICY
      avt profile TELEPRES-AVT-POLICY-DEFAULT id 1
      avt profile TELEPRES-AVT-POLICY-VOICE id 2
   !
   vrf customer14
      avt policy TELEPRES-AVT-POLICY
      avt profile TELEPRES-AVT-POLICY-DEFAULT id 1
      avt profile TELEPRES-AVT-POLICY-VOICE id 2
   !
   vrf customer15
      avt policy TELEPRES-AVT-POLICY
      avt profile TELEPRES-AVT-POLICY-DEFAULT id 1
      avt profile TELEPRES-AVT-POLICY-VOICE id 2
   !
   vrf customer16
      avt policy TELEPRES-AVT-POLICY
      avt profile TELEPRES-AVT-POLICY-DEFAULT id 1
      avt profile TELEPRES-AVT-POLICY-VOICE id 2
   !
   vrf customer17
      avt policy TELEPRES-AVT-POLICY
      avt profile TELEPRES-AVT-POLICY-DEFAULT id 1
      avt profile TELEPRES-AVT-POLICY-VOICE id 2
   !
   vrf customer18
      avt policy TELEPRES-AVT-POLICY
      avt profile TELEPRES-AVT-POLICY-DEFAULT id 1
      avt profile TELEPRES-AVT-POLICY-VOICE id 2
   !
   vrf customer19
      avt policy TELEPRES-AVT-POLICY
      avt profile TELEPRES-AVT-POLICY-DEFAULT id 1
      avt profile TELEPRES-AVT-POLICY-VOICE id 2
   !
   vrf customer20
      avt policy TELEPRES-AVT-POLICY
      avt profile TELEPRES-AVT-POLICY-DEFAULT id 1
      avt profile TELEPRES-AVT-POLICY-VOICE id 2
   !
   vrf customer21
      avt policy TELEPRES-AVT-POLICY
      avt profile TELEPRES-AVT-POLICY-DEFAULT id 1
      avt profile TELEPRES-AVT-POLICY-VOICE id 2
   !
   vrf customer22
      avt policy TELEPRES-AVT-POLICY
      avt profile TELEPRES-AVT-POLICY-DEFAULT id 1
      avt profile TELEPRES-AVT-POLICY-VOICE id 2
   !
   vrf customer23
      avt policy TELEPRES-AVT-POLICY
      avt profile TELEPRES-AVT-POLICY-DEFAULT id 1
      avt profile TELEPRES-AVT-POLICY-VOICE id 2
   !
   vrf customer24
      avt policy TELEPRES-AVT-POLICY
      avt profile TELEPRES-AVT-POLICY-DEFAULT id 1
      avt profile TELEPRES-AVT-POLICY-VOICE id 2
   !
   vrf customer25
      avt policy TELEPRES-AVT-POLICY
      avt profile TELEPRES-AVT-POLICY-DEFAULT id 1
      avt profile TELEPRES-AVT-POLICY-VOICE id 2
   !
   vrf customer26
      avt policy TELEPRES-AVT-POLICY
      avt profile TELEPRES-AVT-POLICY-DEFAULT id 1
      avt profile TELEPRES-AVT-POLICY-VOICE id 2
   !
   vrf customer27
      avt policy TELEPRES-AVT-POLICY
      avt profile TELEPRES-AVT-POLICY-DEFAULT id 1
      avt profile TELEPRES-AVT-POLICY-VOICE id 2
   !
   vrf customer28
      avt policy TELEPRES-AVT-POLICY
      avt profile TELEPRES-AVT-POLICY-DEFAULT id 1
      avt profile TELEPRES-AVT-POLICY-VOICE id 2
   !
   vrf customer29
      avt policy TELEPRES-AVT-POLICY
      avt profile TELEPRES-AVT-POLICY-DEFAULT id 1
      avt profile TELEPRES-AVT-POLICY-VOICE id 2
   !
   vrf customer30
      avt policy TELEPRES-AVT-POLICY
      avt profile TELEPRES-AVT-POLICY-DEFAULT id 1
      avt profile TELEPRES-AVT-POLICY-VOICE id 2
   !
   vrf customer31
      avt policy TELEPRES-AVT-POLICY
      avt profile TELEPRES-AVT-POLICY-DEFAULT id 1
      avt profile TELEPRES-AVT-POLICY-VOICE id 2
   !
   vrf customer32
      avt policy TELEPRES-AVT-POLICY
      avt profile TELEPRES-AVT-POLICY-DEFAULT id 1
      avt profile TELEPRES-AVT-POLICY-VOICE id 2
   !
   vrf customer33
      avt policy TELEPRES-AVT-POLICY
      avt profile TELEPRES-AVT-POLICY-DEFAULT id 1
      avt profile TELEPRES-AVT-POLICY-VOICE id 2
   !
   vrf customer34
      avt policy TELEPRES-AVT-POLICY
      avt profile TELEPRES-AVT-POLICY-DEFAULT id 1
      avt profile TELEPRES-AVT-POLICY-VOICE id 2
   !
   vrf customer35
      avt policy TELEPRES-AVT-POLICY
      avt profile TELEPRES-AVT-POLICY-DEFAULT id 1
      avt profile TELEPRES-AVT-POLICY-VOICE id 2
   !
   vrf customer36
      avt policy TELEPRES-AVT-POLICY
      avt profile TELEPRES-AVT-POLICY-DEFAULT id 1
      avt profile TELEPRES-AVT-POLICY-VOICE id 2
   !
   vrf customer37
      avt policy TELEPRES-AVT-POLICY
      avt profile TELEPRES-AVT-POLICY-DEFAULT id 1
      avt profile TELEPRES-AVT-POLICY-VOICE id 2
   !
   vrf customer38
      avt policy TELEPRES-AVT-POLICY
      avt profile TELEPRES-AVT-POLICY-DEFAULT id 1
      avt profile TELEPRES-AVT-POLICY-VOICE id 2
   !
   vrf customer39
      avt policy TELEPRES-AVT-POLICY
      avt profile TELEPRES-AVT-POLICY-DEFAULT id 1
      avt profile TELEPRES-AVT-POLICY-VOICE id 2
   !
   vrf customer40
      avt policy TELEPRES-AVT-POLICY
      avt profile TELEPRES-AVT-POLICY-DEFAULT id 1
      avt profile TELEPRES-AVT-POLICY-VOICE id 2
   !
   vrf customer41
      avt policy TELEPRES-AVT-POLICY
      avt profile TELEPRES-AVT-POLICY-DEFAULT id 1
      avt profile TELEPRES-AVT-POLICY-VOICE id 2
   !
   vrf customer42
      avt policy TELEPRES-AVT-POLICY
      avt profile TELEPRES-AVT-POLICY-DEFAULT id 1
      avt profile TELEPRES-AVT-POLICY-VOICE id 2
   !
   vrf customer43
      avt policy TELEPRES-AVT-POLICY
      avt profile TELEPRES-AVT-POLICY-DEFAULT id 1
      avt profile TELEPRES-AVT-POLICY-VOICE id 2
   !
   vrf customer44
      avt policy TELEPRES-AVT-POLICY
      avt profile TELEPRES-AVT-POLICY-DEFAULT id 1
      avt profile TELEPRES-AVT-POLICY-VOICE id 2
   !
   vrf customer45
      avt policy TELEPRES-AVT-POLICY
      avt profile TELEPRES-AVT-POLICY-DEFAULT id 1
      avt profile TELEPRES-AVT-POLICY-VOICE id 2
   !
   vrf customer46
      avt policy TELEPRES-AVT-POLICY
      avt profile TELEPRES-AVT-POLICY-DEFAULT id 1
      avt profile TELEPRES-AVT-POLICY-VOICE id 2
   !
   vrf customer47
      avt policy TELEPRES-AVT-POLICY
      avt profile TELEPRES-AVT-POLICY-DEFAULT id 1
      avt profile TELEPRES-AVT-POLICY-VOICE id 2
   !
   vrf customer48
      avt policy TELEPRES-AVT-POLICY
      avt profile TELEPRES-AVT-POLICY-DEFAULT id 1
      avt profile TELEPRES-AVT-POLICY-VOICE id 2
   !
   vrf customer49
      avt policy TELEPRES-AVT-POLICY
      avt profile TELEPRES-AVT-POLICY-DEFAULT id 1
      avt profile TELEPRES-AVT-POLICY-VOICE id 2
   !
   vrf customer50
      avt policy TELEPRES-AVT-POLICY
      avt profile TELEPRES-AVT-POLICY-DEFAULT id 1
      avt profile TELEPRES-AVT-POLICY-VOICE id 2
   !
   vrf customer51
      avt policy TELEPRES-AVT-POLICY
      avt profile TELEPRES-AVT-POLICY-DEFAULT id 1
      avt profile TELEPRES-AVT-POLICY-VOICE id 2
   !
   vrf customer52
      avt policy TELEPRES-AVT-POLICY
      avt profile TELEPRES-AVT-POLICY-DEFAULT id 1
      avt profile TELEPRES-AVT-POLICY-VOICE id 2
   !
   vrf customer53
      avt policy TELEPRES-AVT-POLICY
      avt profile TELEPRES-AVT-POLICY-DEFAULT id 1
      avt profile TELEPRES-AVT-POLICY-VOICE id 2
   !
   vrf customer54
      avt policy TELEPRES-AVT-POLICY
      avt profile TELEPRES-AVT-POLICY-DEFAULT id 1
      avt profile TELEPRES-AVT-POLICY-VOICE id 2
   !
   vrf customer55
      avt policy TELEPRES-AVT-POLICY
      avt profile TELEPRES-AVT-POLICY-DEFAULT id 1
      avt profile TELEPRES-AVT-POLICY-VOICE id 2
   !
   vrf customer56
      avt policy TELEPRES-AVT-POLICY
      avt profile TELEPRES-AVT-POLICY-DEFAULT id 1
      avt profile TELEPRES-AVT-POLICY-VOICE id 2
   !
   vrf customer57
      avt policy TELEPRES-AVT-POLICY
      avt profile TELEPRES-AVT-POLICY-DEFAULT id 1
      avt profile TELEPRES-AVT-POLICY-VOICE id 2
   !
   vrf customer58
      avt policy TELEPRES-AVT-POLICY
      avt profile TELEPRES-AVT-POLICY-DEFAULT id 1
      avt profile TELEPRES-AVT-POLICY-VOICE id 2
   !
   vrf customer59
      avt policy TELEPRES-AVT-POLICY
      avt profile TELEPRES-AVT-POLICY-DEFAULT id 1
      avt profile TELEPRES-AVT-POLICY-VOICE id 2
   !
   vrf customer60
      avt policy TELEPRES-AVT-POLICY
      avt profile TELEPRES-AVT-POLICY-DEFAULT id 1
      avt profile TELEPRES-AVT-POLICY-VOICE id 2
   !
   vrf customer61
      avt policy TELEPRES-AVT-POLICY
      avt profile TELEPRES-AVT-POLICY-DEFAULT id 1
      avt profile TELEPRES-AVT-POLICY-VOICE id 2
   !
   vrf customer62
      avt policy TELEPRES-AVT-POLICY
      avt profile TELEPRES-AVT-POLICY-DEFAULT id 1
      avt profile TELEPRES-AVT-POLICY-VOICE id 2
   !
   vrf customer63
      avt policy TELEPRES-AVT-POLICY
      avt profile TELEPRES-AVT-POLICY-DEFAULT id 1
      avt profile TELEPRES-AVT-POLICY-VOICE id 2
   !
   vrf customer64
      avt policy TELEPRES-AVT-POLICY
      avt profile TELEPRES-AVT-POLICY-DEFAULT id 1
      avt profile TELEPRES-AVT-POLICY-VOICE id 2
   !
   vrf default
      avt policy DEFAULT-AVT-POLICY-WITH-CP
      avt profile DEFAULT-AVT-POLICY-DEFAULT id 1
      avt profile DEFAULT-AVT-POLICY-CONTROL-PLANE id 254
```

### Router Traffic-Engineering

- Traffic Engineering is enabled.

#### Router Traffic Engineering Device Configuration

```eos
!
router traffic-engineering
```

### Router BGP

ASN Notation: asplain

#### Router BGP Summary

| BGP AS | Router ID |
| ------ | --------- |
| 65000 | 10.1.0.2 |

| BGP Tuning |
| ---------- |
| no bgp default ipv4-unicast |
| maximum-paths 16 |

#### Router BGP Peer Groups

##### IPv4-UNDERLAY-PEERS

| Settings | Value |
| -------- | ----- |
| Address Family | ipv4 |
| Send community | all |
| Maximum routes | 12000 |

##### WAN-OVERLAY-PEERS

| Settings | Value |
| -------- | ----- |
| Address Family | wan |
| Remote AS | 65000 |
| Source | Dps1 |
| BFD | True |
| BFD Timers | interval: 1000, min_rx: 1000, multiplier: 10 |
| TTL Max Hops | 1 |
| Send community | all |
| Maximum routes | 0 (no limit) |

#### BGP Neighbors

| Neighbor | Remote AS | VRF | Shutdown | Send-community | Maximum-routes | Allowas-in | BFD | RIB Pre-Policy Retain | Route-Reflector Client | Passive | TTL Max Hops |
| -------- | --------- | --- | -------- | -------------- | -------------- | ---------- | --- | --------------------- | ---------------------- | ------- | ------------ |
| 10.1.0.65 | 65000 | default | - | all | - | - | - | - | True | - | - |
| 10.99.0.2 | 65010 | default | - | Inherited from peer group IPv4-UNDERLAY-PEERS | Inherited from peer group IPv4-UNDERLAY-PEERS | - | - | - | - | - | - |
| 10.255.0.1 | Inherited from peer group WAN-OVERLAY-PEERS | default | - | Inherited from peer group WAN-OVERLAY-PEERS | Inherited from peer group WAN-OVERLAY-PEERS | - | Inherited from peer group WAN-OVERLAY-PEERS(interval: 1000, min_rx: 1000, multiplier: 10) | - | - | - | Inherited from peer group WAN-OVERLAY-PEERS |
| 10.255.0.2 | Inherited from peer group WAN-OVERLAY-PEERS | default | - | Inherited from peer group WAN-OVERLAY-PEERS | Inherited from peer group WAN-OVERLAY-PEERS | - | Inherited from peer group WAN-OVERLAY-PEERS(interval: 1000, min_rx: 1000, multiplier: 10) | - | - | - | Inherited from peer group WAN-OVERLAY-PEERS |
| 10.99.0.2 | 65010 | customer1 | - | Inherited from peer group IPv4-UNDERLAY-PEERS | Inherited from peer group IPv4-UNDERLAY-PEERS | - | - | - | - | - | - |
| 10.99.0.2 | 65010 | customer2 | - | Inherited from peer group IPv4-UNDERLAY-PEERS | Inherited from peer group IPv4-UNDERLAY-PEERS | - | - | - | - | - | - |
| 10.99.0.2 | 65010 | customer3 | - | Inherited from peer group IPv4-UNDERLAY-PEERS | Inherited from peer group IPv4-UNDERLAY-PEERS | - | - | - | - | - | - |
| 10.99.0.2 | 65010 | customer4 | - | Inherited from peer group IPv4-UNDERLAY-PEERS | Inherited from peer group IPv4-UNDERLAY-PEERS | - | - | - | - | - | - |
| 10.99.0.2 | 65010 | customer5 | - | Inherited from peer group IPv4-UNDERLAY-PEERS | Inherited from peer group IPv4-UNDERLAY-PEERS | - | - | - | - | - | - |
| 10.99.0.2 | 65010 | customer6 | - | Inherited from peer group IPv4-UNDERLAY-PEERS | Inherited from peer group IPv4-UNDERLAY-PEERS | - | - | - | - | - | - |
| 10.99.0.2 | 65010 | customer7 | - | Inherited from peer group IPv4-UNDERLAY-PEERS | Inherited from peer group IPv4-UNDERLAY-PEERS | - | - | - | - | - | - |
| 10.99.0.2 | 65010 | customer8 | - | Inherited from peer group IPv4-UNDERLAY-PEERS | Inherited from peer group IPv4-UNDERLAY-PEERS | - | - | - | - | - | - |
| 10.99.0.2 | 65010 | customer9 | - | Inherited from peer group IPv4-UNDERLAY-PEERS | Inherited from peer group IPv4-UNDERLAY-PEERS | - | - | - | - | - | - |
| 10.99.0.2 | 65010 | customer10 | - | Inherited from peer group IPv4-UNDERLAY-PEERS | Inherited from peer group IPv4-UNDERLAY-PEERS | - | - | - | - | - | - |
| 10.99.0.2 | 65010 | customer11 | - | Inherited from peer group IPv4-UNDERLAY-PEERS | Inherited from peer group IPv4-UNDERLAY-PEERS | - | - | - | - | - | - |
| 10.99.0.2 | 65010 | customer12 | - | Inherited from peer group IPv4-UNDERLAY-PEERS | Inherited from peer group IPv4-UNDERLAY-PEERS | - | - | - | - | - | - |
| 10.99.0.2 | 65010 | customer13 | - | Inherited from peer group IPv4-UNDERLAY-PEERS | Inherited from peer group IPv4-UNDERLAY-PEERS | - | - | - | - | - | - |
| 10.99.0.2 | 65010 | customer14 | - | Inherited from peer group IPv4-UNDERLAY-PEERS | Inherited from peer group IPv4-UNDERLAY-PEERS | - | - | - | - | - | - |
| 10.99.0.2 | 65010 | customer15 | - | Inherited from peer group IPv4-UNDERLAY-PEERS | Inherited from peer group IPv4-UNDERLAY-PEERS | - | - | - | - | - | - |
| 10.99.0.2 | 65010 | customer16 | - | Inherited from peer group IPv4-UNDERLAY-PEERS | Inherited from peer group IPv4-UNDERLAY-PEERS | - | - | - | - | - | - |
| 10.99.0.2 | 65010 | customer17 | - | Inherited from peer group IPv4-UNDERLAY-PEERS | Inherited from peer group IPv4-UNDERLAY-PEERS | - | - | - | - | - | - |
| 10.99.0.2 | 65010 | customer18 | - | Inherited from peer group IPv4-UNDERLAY-PEERS | Inherited from peer group IPv4-UNDERLAY-PEERS | - | - | - | - | - | - |
| 10.99.0.2 | 65010 | customer19 | - | Inherited from peer group IPv4-UNDERLAY-PEERS | Inherited from peer group IPv4-UNDERLAY-PEERS | - | - | - | - | - | - |
| 10.99.0.2 | 65010 | customer20 | - | Inherited from peer group IPv4-UNDERLAY-PEERS | Inherited from peer group IPv4-UNDERLAY-PEERS | - | - | - | - | - | - |
| 10.99.0.2 | 65010 | customer21 | - | Inherited from peer group IPv4-UNDERLAY-PEERS | Inherited from peer group IPv4-UNDERLAY-PEERS | - | - | - | - | - | - |
| 10.99.0.2 | 65010 | customer22 | - | Inherited from peer group IPv4-UNDERLAY-PEERS | Inherited from peer group IPv4-UNDERLAY-PEERS | - | - | - | - | - | - |
| 10.99.0.2 | 65010 | customer23 | - | Inherited from peer group IPv4-UNDERLAY-PEERS | Inherited from peer group IPv4-UNDERLAY-PEERS | - | - | - | - | - | - |
| 10.99.0.2 | 65010 | customer24 | - | Inherited from peer group IPv4-UNDERLAY-PEERS | Inherited from peer group IPv4-UNDERLAY-PEERS | - | - | - | - | - | - |
| 10.99.0.2 | 65010 | customer25 | - | Inherited from peer group IPv4-UNDERLAY-PEERS | Inherited from peer group IPv4-UNDERLAY-PEERS | - | - | - | - | - | - |
| 10.99.0.2 | 65010 | customer26 | - | Inherited from peer group IPv4-UNDERLAY-PEERS | Inherited from peer group IPv4-UNDERLAY-PEERS | - | - | - | - | - | - |
| 10.99.0.2 | 65010 | customer27 | - | Inherited from peer group IPv4-UNDERLAY-PEERS | Inherited from peer group IPv4-UNDERLAY-PEERS | - | - | - | - | - | - |
| 10.99.0.2 | 65010 | customer28 | - | Inherited from peer group IPv4-UNDERLAY-PEERS | Inherited from peer group IPv4-UNDERLAY-PEERS | - | - | - | - | - | - |
| 10.99.0.2 | 65010 | customer29 | - | Inherited from peer group IPv4-UNDERLAY-PEERS | Inherited from peer group IPv4-UNDERLAY-PEERS | - | - | - | - | - | - |
| 10.99.0.2 | 65010 | customer30 | - | Inherited from peer group IPv4-UNDERLAY-PEERS | Inherited from peer group IPv4-UNDERLAY-PEERS | - | - | - | - | - | - |
| 10.99.0.2 | 65010 | customer31 | - | Inherited from peer group IPv4-UNDERLAY-PEERS | Inherited from peer group IPv4-UNDERLAY-PEERS | - | - | - | - | - | - |
| 10.99.0.2 | 65010 | customer32 | - | Inherited from peer group IPv4-UNDERLAY-PEERS | Inherited from peer group IPv4-UNDERLAY-PEERS | - | - | - | - | - | - |
| 10.99.0.2 | 65010 | customer33 | - | Inherited from peer group IPv4-UNDERLAY-PEERS | Inherited from peer group IPv4-UNDERLAY-PEERS | - | - | - | - | - | - |
| 10.99.0.2 | 65010 | customer34 | - | Inherited from peer group IPv4-UNDERLAY-PEERS | Inherited from peer group IPv4-UNDERLAY-PEERS | - | - | - | - | - | - |
| 10.99.0.2 | 65010 | customer35 | - | Inherited from peer group IPv4-UNDERLAY-PEERS | Inherited from peer group IPv4-UNDERLAY-PEERS | - | - | - | - | - | - |
| 10.99.0.2 | 65010 | customer36 | - | Inherited from peer group IPv4-UNDERLAY-PEERS | Inherited from peer group IPv4-UNDERLAY-PEERS | - | - | - | - | - | - |
| 10.99.0.2 | 65010 | customer37 | - | Inherited from peer group IPv4-UNDERLAY-PEERS | Inherited from peer group IPv4-UNDERLAY-PEERS | - | - | - | - | - | - |
| 10.99.0.2 | 65010 | customer38 | - | Inherited from peer group IPv4-UNDERLAY-PEERS | Inherited from peer group IPv4-UNDERLAY-PEERS | - | - | - | - | - | - |
| 10.99.0.2 | 65010 | customer39 | - | Inherited from peer group IPv4-UNDERLAY-PEERS | Inherited from peer group IPv4-UNDERLAY-PEERS | - | - | - | - | - | - |
| 10.99.0.2 | 65010 | customer40 | - | Inherited from peer group IPv4-UNDERLAY-PEERS | Inherited from peer group IPv4-UNDERLAY-PEERS | - | - | - | - | - | - |
| 10.99.0.2 | 65010 | customer41 | - | Inherited from peer group IPv4-UNDERLAY-PEERS | Inherited from peer group IPv4-UNDERLAY-PEERS | - | - | - | - | - | - |
| 10.99.0.2 | 65010 | customer42 | - | Inherited from peer group IPv4-UNDERLAY-PEERS | Inherited from peer group IPv4-UNDERLAY-PEERS | - | - | - | - | - | - |
| 10.99.0.2 | 65010 | customer43 | - | Inherited from peer group IPv4-UNDERLAY-PEERS | Inherited from peer group IPv4-UNDERLAY-PEERS | - | - | - | - | - | - |
| 10.99.0.2 | 65010 | customer44 | - | Inherited from peer group IPv4-UNDERLAY-PEERS | Inherited from peer group IPv4-UNDERLAY-PEERS | - | - | - | - | - | - |
| 10.99.0.2 | 65010 | customer45 | - | Inherited from peer group IPv4-UNDERLAY-PEERS | Inherited from peer group IPv4-UNDERLAY-PEERS | - | - | - | - | - | - |
| 10.99.0.2 | 65010 | customer46 | - | Inherited from peer group IPv4-UNDERLAY-PEERS | Inherited from peer group IPv4-UNDERLAY-PEERS | - | - | - | - | - | - |
| 10.99.0.2 | 65010 | customer47 | - | Inherited from peer group IPv4-UNDERLAY-PEERS | Inherited from peer group IPv4-UNDERLAY-PEERS | - | - | - | - | - | - |
| 10.99.0.2 | 65010 | customer48 | - | Inherited from peer group IPv4-UNDERLAY-PEERS | Inherited from peer group IPv4-UNDERLAY-PEERS | - | - | - | - | - | - |
| 10.99.0.2 | 65010 | customer49 | - | Inherited from peer group IPv4-UNDERLAY-PEERS | Inherited from peer group IPv4-UNDERLAY-PEERS | - | - | - | - | - | - |
| 10.99.0.2 | 65010 | customer50 | - | Inherited from peer group IPv4-UNDERLAY-PEERS | Inherited from peer group IPv4-UNDERLAY-PEERS | - | - | - | - | - | - |
| 10.99.0.2 | 65010 | customer51 | - | Inherited from peer group IPv4-UNDERLAY-PEERS | Inherited from peer group IPv4-UNDERLAY-PEERS | - | - | - | - | - | - |
| 10.99.0.2 | 65010 | customer52 | - | Inherited from peer group IPv4-UNDERLAY-PEERS | Inherited from peer group IPv4-UNDERLAY-PEERS | - | - | - | - | - | - |
| 10.99.0.2 | 65010 | customer53 | - | Inherited from peer group IPv4-UNDERLAY-PEERS | Inherited from peer group IPv4-UNDERLAY-PEERS | - | - | - | - | - | - |
| 10.99.0.2 | 65010 | customer54 | - | Inherited from peer group IPv4-UNDERLAY-PEERS | Inherited from peer group IPv4-UNDERLAY-PEERS | - | - | - | - | - | - |
| 10.99.0.2 | 65010 | customer55 | - | Inherited from peer group IPv4-UNDERLAY-PEERS | Inherited from peer group IPv4-UNDERLAY-PEERS | - | - | - | - | - | - |
| 10.99.0.2 | 65010 | customer56 | - | Inherited from peer group IPv4-UNDERLAY-PEERS | Inherited from peer group IPv4-UNDERLAY-PEERS | - | - | - | - | - | - |
| 10.99.0.2 | 65010 | customer57 | - | Inherited from peer group IPv4-UNDERLAY-PEERS | Inherited from peer group IPv4-UNDERLAY-PEERS | - | - | - | - | - | - |
| 10.99.0.2 | 65010 | customer58 | - | Inherited from peer group IPv4-UNDERLAY-PEERS | Inherited from peer group IPv4-UNDERLAY-PEERS | - | - | - | - | - | - |
| 10.99.0.2 | 65010 | customer59 | - | Inherited from peer group IPv4-UNDERLAY-PEERS | Inherited from peer group IPv4-UNDERLAY-PEERS | - | - | - | - | - | - |
| 10.99.0.2 | 65010 | customer60 | - | Inherited from peer group IPv4-UNDERLAY-PEERS | Inherited from peer group IPv4-UNDERLAY-PEERS | - | - | - | - | - | - |
| 10.99.0.2 | 65010 | customer61 | - | Inherited from peer group IPv4-UNDERLAY-PEERS | Inherited from peer group IPv4-UNDERLAY-PEERS | - | - | - | - | - | - |
| 10.99.0.2 | 65010 | customer62 | - | Inherited from peer group IPv4-UNDERLAY-PEERS | Inherited from peer group IPv4-UNDERLAY-PEERS | - | - | - | - | - | - |
| 10.99.0.2 | 65010 | customer63 | - | Inherited from peer group IPv4-UNDERLAY-PEERS | Inherited from peer group IPv4-UNDERLAY-PEERS | - | - | - | - | - | - |
| 10.99.0.2 | 65010 | customer64 | - | Inherited from peer group IPv4-UNDERLAY-PEERS | Inherited from peer group IPv4-UNDERLAY-PEERS | - | - | - | - | - | - |

#### Router BGP EVPN Address Family

##### EVPN Peer Groups

| Peer Group | Activate | Encapsulation |
| ---------- | -------- | ------------- |
| WAN-OVERLAY-PEERS | True | default |

##### EVPN DCI Gateway Summary

| Settings | Value |
| -------- | ----- |
| L3 Gateway Configured | True |

#### Router BGP IPv4 SR-TE Address Family

##### IPv4 SR-TE Peer Groups

| Peer Group | Activate | Route-map In | Route-map Out |
| ---------- | -------- | ------------ | ------------- |
| WAN-OVERLAY-PEERS | True | - | - |

#### Router BGP Link-State Address Family

##### Link-State Peer Groups

| Peer Group | Activate | Missing policy In action | Missing policy Out action |
| ---------- | -------- | ------------------------ | ------------------------- |
| WAN-OVERLAY-PEERS | True | - | - |

##### Link-State Path Selection Configuration

| Settings | Value |
| -------- | ----- |
| Role(s) | producer |

#### Router BGP Path-Selection Address Family

##### Path-Selection Peer Groups

| Peer Group | Activate |
| ---------- | -------- |
| WAN-OVERLAY-PEERS | True |

#### Router BGP VRFs

| VRF | Route-Distinguisher | Redistribute |
| --- | ------------------- | ------------ |
| customer1 | 10.1.0.2:254 | connected |
| customer2 | 10.1.0.2:2 | connected |
| customer3 | 10.1.0.2:3 | connected |
| customer4 | 10.1.0.2:4 | connected |
| customer5 | 10.1.0.2:5 | connected |
| customer6 | 10.1.0.2:6 | connected |
| customer7 | 10.1.0.2:7 | connected |
| customer8 | 10.1.0.2:8 | connected |
| customer9 | 10.1.0.2:9 | connected |
| customer10 | 10.1.0.2:10 | connected |
| customer11 | 10.1.0.2:11 | connected |
| customer12 | 10.1.0.2:12 | connected |
| customer13 | 10.1.0.2:13 | connected |
| customer14 | 10.1.0.2:14 | connected |
| customer15 | 10.1.0.2:15 | connected |
| customer16 | 10.1.0.2:16 | connected |
| customer17 | 10.1.0.2:17 | connected |
| customer18 | 10.1.0.2:18 | connected |
| customer19 | 10.1.0.2:19 | connected |
| customer20 | 10.1.0.2:20 | connected |
| customer21 | 10.1.0.2:21 | connected |
| customer22 | 10.1.0.2:22 | connected |
| customer23 | 10.1.0.2:23 | connected |
| customer24 | 10.1.0.2:24 | connected |
| customer25 | 10.1.0.2:25 | connected |
| customer26 | 10.1.0.2:26 | connected |
| customer27 | 10.1.0.2:27 | connected |
| customer28 | 10.1.0.2:28 | connected |
| customer29 | 10.1.0.2:29 | connected |
| customer30 | 10.1.0.2:30 | connected |
| customer31 | 10.1.0.2:31 | connected |
| customer32 | 10.1.0.2:32 | connected |
| customer33 | 10.1.0.2:33 | connected |
| customer34 | 10.1.0.2:34 | connected |
| customer35 | 10.1.0.2:35 | connected |
| customer36 | 10.1.0.2:36 | connected |
| customer37 | 10.1.0.2:37 | connected |
| customer38 | 10.1.0.2:38 | connected |
| customer39 | 10.1.0.2:39 | connected |
| customer40 | 10.1.0.2:40 | connected |
| customer41 | 10.1.0.2:41 | connected |
| customer42 | 10.1.0.2:42 | connected |
| customer43 | 10.1.0.2:43 | connected |
| customer44 | 10.1.0.2:44 | connected |
| customer45 | 10.1.0.2:45 | connected |
| customer46 | 10.1.0.2:46 | connected |
| customer47 | 10.1.0.2:47 | connected |
| customer48 | 10.1.0.2:48 | connected |
| customer49 | 10.1.0.2:49 | connected |
| customer50 | 10.1.0.2:50 | connected |
| customer51 | 10.1.0.2:51 | connected |
| customer52 | 10.1.0.2:52 | connected |
| customer53 | 10.1.0.2:53 | connected |
| customer54 | 10.1.0.2:54 | connected |
| customer55 | 10.1.0.2:55 | connected |
| customer56 | 10.1.0.2:56 | connected |
| customer57 | 10.1.0.2:57 | connected |
| customer58 | 10.1.0.2:58 | connected |
| customer59 | 10.1.0.2:59 | connected |
| customer60 | 10.1.0.2:60 | connected |
| customer61 | 10.1.0.2:61 | connected |
| customer62 | 10.1.0.2:62 | connected |
| customer63 | 10.1.0.2:63 | connected |
| customer64 | 10.1.0.2:64 | connected |
| default | 10.1.0.2:1 | - |

#### Router BGP Device Configuration

```eos
!
router bgp 65000
   router-id 10.1.0.2
   maximum-paths 16
   no bgp default ipv4-unicast
   neighbor IPv4-UNDERLAY-PEERS peer group
   neighbor IPv4-UNDERLAY-PEERS send-community
   neighbor IPv4-UNDERLAY-PEERS maximum-routes 12000
   neighbor IPv4-UNDERLAY-PEERS route-map RM-BGP-UNDERLAY-PEERS-IN in
   neighbor IPv4-UNDERLAY-PEERS route-map RM-BGP-UNDERLAY-PEERS-OUT out
   neighbor WAN-OVERLAY-PEERS peer group
   neighbor WAN-OVERLAY-PEERS remote-as 65000
   neighbor WAN-OVERLAY-PEERS update-source Dps1
   neighbor WAN-OVERLAY-PEERS bfd
   neighbor WAN-OVERLAY-PEERS bfd interval 1000 min-rx 1000 multiplier 10
   neighbor WAN-OVERLAY-PEERS ttl maximum-hops 1
   neighbor WAN-OVERLAY-PEERS password 7 <removed>
   neighbor WAN-OVERLAY-PEERS send-community
   neighbor WAN-OVERLAY-PEERS maximum-routes 0
   neighbor 10.1.0.65 remote-as 65000
   neighbor 10.1.0.65 description r1-hub-wan1
   neighbor 10.1.0.65 route-reflector-client
   neighbor 10.1.0.65 update-source Dps1
   neighbor 10.1.0.65 route-map RM-WAN-HA-PEER-IN in
   neighbor 10.1.0.65 route-map RM-WAN-HA-PEER-OUT out
   neighbor 10.1.0.65 send-community
   neighbor 10.99.0.2 peer group IPv4-UNDERLAY-PEERS
   neighbor 10.99.0.2 remote-as 65010
   neighbor 10.99.0.2 description r1-hub-leaf1_Ethernet3
   neighbor 10.255.0.1 peer group WAN-OVERLAY-PEERS
   neighbor 10.255.0.1 description pf1
   neighbor 10.255.0.2 peer group WAN-OVERLAY-PEERS
   neighbor 10.255.0.2 description pf2
   redistribute connected route-map RM-CONN-2-BGP
   !
   address-family evpn
      neighbor WAN-OVERLAY-PEERS route-map RM-EVPN-SOO-IN in
      neighbor WAN-OVERLAY-PEERS route-map RM-EVPN-SOO-OUT out
      neighbor WAN-OVERLAY-PEERS activate
      neighbor 10.1.0.65 activate
      neighbor default next-hop-self received-evpn-routes route-type ip-prefix
   !
   address-family ipv4
      neighbor IPv4-UNDERLAY-PEERS activate
      no neighbor WAN-OVERLAY-PEERS activate
   !
   address-family ipv4 sr-te
      neighbor WAN-OVERLAY-PEERS activate
   !
   address-family link-state
      neighbor WAN-OVERLAY-PEERS activate
      path-selection
   !
   address-family path-selection
      bgp additional-paths receive
      bgp additional-paths send any
      neighbor WAN-OVERLAY-PEERS activate
   !
   vrf customer1
      rd 10.1.0.2:254
      route-target import evpn 254:254
      route-target export evpn 254:254
      router-id 10.1.0.2
      neighbor 10.99.0.2 remote-as 65010
      neighbor 10.99.0.2 peer group IPv4-UNDERLAY-PEERS
      neighbor 10.99.0.2 description r1-hub-leaf1_Ethernet3.254_vrf_customer1
      redistribute connected
   !
   vrf customer2
      rd 10.1.0.2:2
      route-target import evpn 2:2
      route-target export evpn 2:2
      router-id 10.1.0.2
      neighbor 10.99.0.2 remote-as 65010
      neighbor 10.99.0.2 peer group IPv4-UNDERLAY-PEERS
      neighbor 10.99.0.2 description r1-hub-leaf1_Ethernet3.2_vrf_customer2
      redistribute connected
   !
   vrf customer3
      rd 10.1.0.2:3
      route-target import evpn 3:3
      route-target export evpn 3:3
      router-id 10.1.0.2
      neighbor 10.99.0.2 remote-as 65010
      neighbor 10.99.0.2 peer group IPv4-UNDERLAY-PEERS
      neighbor 10.99.0.2 description r1-hub-leaf1_Ethernet3.3_vrf_customer3
      redistribute connected
   !
   vrf customer4
      rd 10.1.0.2:4
      route-target import evpn 4:4
      route-target export evpn 4:4
      router-id 10.1.0.2
      neighbor 10.99.0.2 remote-as 65010
      neighbor 10.99.0.2 peer group IPv4-UNDERLAY-PEERS
      neighbor 10.99.0.2 description r1-hub-leaf1_Ethernet3.4_vrf_customer4
      redistribute connected
   !
   vrf customer5
      rd 10.1.0.2:5
      route-target import evpn 5:5
      route-target export evpn 5:5
      router-id 10.1.0.2
      neighbor 10.99.0.2 remote-as 65010
      neighbor 10.99.0.2 peer group IPv4-UNDERLAY-PEERS
      neighbor 10.99.0.2 description r1-hub-leaf1_Ethernet3.5_vrf_customer5
      redistribute connected
   !
   vrf customer6
      rd 10.1.0.2:6
      route-target import evpn 6:6
      route-target export evpn 6:6
      router-id 10.1.0.2
      neighbor 10.99.0.2 remote-as 65010
      neighbor 10.99.0.2 peer group IPv4-UNDERLAY-PEERS
      neighbor 10.99.0.2 description r1-hub-leaf1_Ethernet3.6_vrf_customer6
      redistribute connected
   !
   vrf customer7
      rd 10.1.0.2:7
      route-target import evpn 7:7
      route-target export evpn 7:7
      router-id 10.1.0.2
      neighbor 10.99.0.2 remote-as 65010
      neighbor 10.99.0.2 peer group IPv4-UNDERLAY-PEERS
      neighbor 10.99.0.2 description r1-hub-leaf1_Ethernet3.7_vrf_customer7
      redistribute connected
   !
   vrf customer8
      rd 10.1.0.2:8
      route-target import evpn 8:8
      route-target export evpn 8:8
      router-id 10.1.0.2
      neighbor 10.99.0.2 remote-as 65010
      neighbor 10.99.0.2 peer group IPv4-UNDERLAY-PEERS
      neighbor 10.99.0.2 description r1-hub-leaf1_Ethernet3.8_vrf_customer8
      redistribute connected
   !
   vrf customer9
      rd 10.1.0.2:9
      route-target import evpn 9:9
      route-target export evpn 9:9
      router-id 10.1.0.2
      neighbor 10.99.0.2 remote-as 65010
      neighbor 10.99.0.2 peer group IPv4-UNDERLAY-PEERS
      neighbor 10.99.0.2 description r1-hub-leaf1_Ethernet3.9_vrf_customer9
      redistribute connected
   !
   vrf customer10
      rd 10.1.0.2:10
      route-target import evpn 10:10
      route-target export evpn 10:10
      router-id 10.1.0.2
      neighbor 10.99.0.2 remote-as 65010
      neighbor 10.99.0.2 peer group IPv4-UNDERLAY-PEERS
      neighbor 10.99.0.2 description r1-hub-leaf1_Ethernet3.10_vrf_customer10
      redistribute connected
   !
   vrf customer11
      rd 10.1.0.2:11
      route-target import evpn 11:11
      route-target export evpn 11:11
      router-id 10.1.0.2
      neighbor 10.99.0.2 remote-as 65010
      neighbor 10.99.0.2 peer group IPv4-UNDERLAY-PEERS
      neighbor 10.99.0.2 description r1-hub-leaf1_Ethernet3.11_vrf_customer11
      redistribute connected
   !
   vrf customer12
      rd 10.1.0.2:12
      route-target import evpn 12:12
      route-target export evpn 12:12
      router-id 10.1.0.2
      neighbor 10.99.0.2 remote-as 65010
      neighbor 10.99.0.2 peer group IPv4-UNDERLAY-PEERS
      neighbor 10.99.0.2 description r1-hub-leaf1_Ethernet3.12_vrf_customer12
      redistribute connected
   !
   vrf customer13
      rd 10.1.0.2:13
      route-target import evpn 13:13
      route-target export evpn 13:13
      router-id 10.1.0.2
      neighbor 10.99.0.2 remote-as 65010
      neighbor 10.99.0.2 peer group IPv4-UNDERLAY-PEERS
      neighbor 10.99.0.2 description r1-hub-leaf1_Ethernet3.13_vrf_customer13
      redistribute connected
   !
   vrf customer14
      rd 10.1.0.2:14
      route-target import evpn 14:14
      route-target export evpn 14:14
      router-id 10.1.0.2
      neighbor 10.99.0.2 remote-as 65010
      neighbor 10.99.0.2 peer group IPv4-UNDERLAY-PEERS
      neighbor 10.99.0.2 description r1-hub-leaf1_Ethernet3.14_vrf_customer14
      redistribute connected
   !
   vrf customer15
      rd 10.1.0.2:15
      route-target import evpn 15:15
      route-target export evpn 15:15
      router-id 10.1.0.2
      neighbor 10.99.0.2 remote-as 65010
      neighbor 10.99.0.2 peer group IPv4-UNDERLAY-PEERS
      neighbor 10.99.0.2 description r1-hub-leaf1_Ethernet3.15_vrf_customer15
      redistribute connected
   !
   vrf customer16
      rd 10.1.0.2:16
      route-target import evpn 16:16
      route-target export evpn 16:16
      router-id 10.1.0.2
      neighbor 10.99.0.2 remote-as 65010
      neighbor 10.99.0.2 peer group IPv4-UNDERLAY-PEERS
      neighbor 10.99.0.2 description r1-hub-leaf1_Ethernet3.16_vrf_customer16
      redistribute connected
   !
   vrf customer17
      rd 10.1.0.2:17
      route-target import evpn 17:17
      route-target export evpn 17:17
      router-id 10.1.0.2
      neighbor 10.99.0.2 remote-as 65010
      neighbor 10.99.0.2 peer group IPv4-UNDERLAY-PEERS
      neighbor 10.99.0.2 description r1-hub-leaf1_Ethernet3.17_vrf_customer17
      redistribute connected
   !
   vrf customer18
      rd 10.1.0.2:18
      route-target import evpn 18:18
      route-target export evpn 18:18
      router-id 10.1.0.2
      neighbor 10.99.0.2 remote-as 65010
      neighbor 10.99.0.2 peer group IPv4-UNDERLAY-PEERS
      neighbor 10.99.0.2 description r1-hub-leaf1_Ethernet3.18_vrf_customer18
      redistribute connected
   !
   vrf customer19
      rd 10.1.0.2:19
      route-target import evpn 19:19
      route-target export evpn 19:19
      router-id 10.1.0.2
      neighbor 10.99.0.2 remote-as 65010
      neighbor 10.99.0.2 peer group IPv4-UNDERLAY-PEERS
      neighbor 10.99.0.2 description r1-hub-leaf1_Ethernet3.19_vrf_customer19
      redistribute connected
   !
   vrf customer20
      rd 10.1.0.2:20
      route-target import evpn 20:20
      route-target export evpn 20:20
      router-id 10.1.0.2
      neighbor 10.99.0.2 remote-as 65010
      neighbor 10.99.0.2 peer group IPv4-UNDERLAY-PEERS
      neighbor 10.99.0.2 description r1-hub-leaf1_Ethernet3.20_vrf_customer20
      redistribute connected
   !
   vrf customer21
      rd 10.1.0.2:21
      route-target import evpn 21:21
      route-target export evpn 21:21
      router-id 10.1.0.2
      neighbor 10.99.0.2 remote-as 65010
      neighbor 10.99.0.2 peer group IPv4-UNDERLAY-PEERS
      neighbor 10.99.0.2 description r1-hub-leaf1_Ethernet3.21_vrf_customer21
      redistribute connected
   !
   vrf customer22
      rd 10.1.0.2:22
      route-target import evpn 22:22
      route-target export evpn 22:22
      router-id 10.1.0.2
      neighbor 10.99.0.2 remote-as 65010
      neighbor 10.99.0.2 peer group IPv4-UNDERLAY-PEERS
      neighbor 10.99.0.2 description r1-hub-leaf1_Ethernet3.22_vrf_customer22
      redistribute connected
   !
   vrf customer23
      rd 10.1.0.2:23
      route-target import evpn 23:23
      route-target export evpn 23:23
      router-id 10.1.0.2
      neighbor 10.99.0.2 remote-as 65010
      neighbor 10.99.0.2 peer group IPv4-UNDERLAY-PEERS
      neighbor 10.99.0.2 description r1-hub-leaf1_Ethernet3.23_vrf_customer23
      redistribute connected
   !
   vrf customer24
      rd 10.1.0.2:24
      route-target import evpn 24:24
      route-target export evpn 24:24
      router-id 10.1.0.2
      neighbor 10.99.0.2 remote-as 65010
      neighbor 10.99.0.2 peer group IPv4-UNDERLAY-PEERS
      neighbor 10.99.0.2 description r1-hub-leaf1_Ethernet3.24_vrf_customer24
      redistribute connected
   !
   vrf customer25
      rd 10.1.0.2:25
      route-target import evpn 25:25
      route-target export evpn 25:25
      router-id 10.1.0.2
      neighbor 10.99.0.2 remote-as 65010
      neighbor 10.99.0.2 peer group IPv4-UNDERLAY-PEERS
      neighbor 10.99.0.2 description r1-hub-leaf1_Ethernet3.25_vrf_customer25
      redistribute connected
   !
   vrf customer26
      rd 10.1.0.2:26
      route-target import evpn 26:26
      route-target export evpn 26:26
      router-id 10.1.0.2
      neighbor 10.99.0.2 remote-as 65010
      neighbor 10.99.0.2 peer group IPv4-UNDERLAY-PEERS
      neighbor 10.99.0.2 description r1-hub-leaf1_Ethernet3.26_vrf_customer26
      redistribute connected
   !
   vrf customer27
      rd 10.1.0.2:27
      route-target import evpn 27:27
      route-target export evpn 27:27
      router-id 10.1.0.2
      neighbor 10.99.0.2 remote-as 65010
      neighbor 10.99.0.2 peer group IPv4-UNDERLAY-PEERS
      neighbor 10.99.0.2 description r1-hub-leaf1_Ethernet3.27_vrf_customer27
      redistribute connected
   !
   vrf customer28
      rd 10.1.0.2:28
      route-target import evpn 28:28
      route-target export evpn 28:28
      router-id 10.1.0.2
      neighbor 10.99.0.2 remote-as 65010
      neighbor 10.99.0.2 peer group IPv4-UNDERLAY-PEERS
      neighbor 10.99.0.2 description r1-hub-leaf1_Ethernet3.28_vrf_customer28
      redistribute connected
   !
   vrf customer29
      rd 10.1.0.2:29
      route-target import evpn 29:29
      route-target export evpn 29:29
      router-id 10.1.0.2
      neighbor 10.99.0.2 remote-as 65010
      neighbor 10.99.0.2 peer group IPv4-UNDERLAY-PEERS
      neighbor 10.99.0.2 description r1-hub-leaf1_Ethernet3.29_vrf_customer29
      redistribute connected
   !
   vrf customer30
      rd 10.1.0.2:30
      route-target import evpn 30:30
      route-target export evpn 30:30
      router-id 10.1.0.2
      neighbor 10.99.0.2 remote-as 65010
      neighbor 10.99.0.2 peer group IPv4-UNDERLAY-PEERS
      neighbor 10.99.0.2 description r1-hub-leaf1_Ethernet3.30_vrf_customer30
      redistribute connected
   !
   vrf customer31
      rd 10.1.0.2:31
      route-target import evpn 31:31
      route-target export evpn 31:31
      router-id 10.1.0.2
      neighbor 10.99.0.2 remote-as 65010
      neighbor 10.99.0.2 peer group IPv4-UNDERLAY-PEERS
      neighbor 10.99.0.2 description r1-hub-leaf1_Ethernet3.31_vrf_customer31
      redistribute connected
   !
   vrf customer32
      rd 10.1.0.2:32
      route-target import evpn 32:32
      route-target export evpn 32:32
      router-id 10.1.0.2
      neighbor 10.99.0.2 remote-as 65010
      neighbor 10.99.0.2 peer group IPv4-UNDERLAY-PEERS
      neighbor 10.99.0.2 description r1-hub-leaf1_Ethernet3.32_vrf_customer32
      redistribute connected
   !
   vrf customer33
      rd 10.1.0.2:33
      route-target import evpn 33:33
      route-target export evpn 33:33
      router-id 10.1.0.2
      neighbor 10.99.0.2 remote-as 65010
      neighbor 10.99.0.2 peer group IPv4-UNDERLAY-PEERS
      neighbor 10.99.0.2 description r1-hub-leaf1_Ethernet3.33_vrf_customer33
      redistribute connected
   !
   vrf customer34
      rd 10.1.0.2:34
      route-target import evpn 34:34
      route-target export evpn 34:34
      router-id 10.1.0.2
      neighbor 10.99.0.2 remote-as 65010
      neighbor 10.99.0.2 peer group IPv4-UNDERLAY-PEERS
      neighbor 10.99.0.2 description r1-hub-leaf1_Ethernet3.34_vrf_customer34
      redistribute connected
   !
   vrf customer35
      rd 10.1.0.2:35
      route-target import evpn 35:35
      route-target export evpn 35:35
      router-id 10.1.0.2
      neighbor 10.99.0.2 remote-as 65010
      neighbor 10.99.0.2 peer group IPv4-UNDERLAY-PEERS
      neighbor 10.99.0.2 description r1-hub-leaf1_Ethernet3.35_vrf_customer35
      redistribute connected
   !
   vrf customer36
      rd 10.1.0.2:36
      route-target import evpn 36:36
      route-target export evpn 36:36
      router-id 10.1.0.2
      neighbor 10.99.0.2 remote-as 65010
      neighbor 10.99.0.2 peer group IPv4-UNDERLAY-PEERS
      neighbor 10.99.0.2 description r1-hub-leaf1_Ethernet3.36_vrf_customer36
      redistribute connected
   !
   vrf customer37
      rd 10.1.0.2:37
      route-target import evpn 37:37
      route-target export evpn 37:37
      router-id 10.1.0.2
      neighbor 10.99.0.2 remote-as 65010
      neighbor 10.99.0.2 peer group IPv4-UNDERLAY-PEERS
      neighbor 10.99.0.2 description r1-hub-leaf1_Ethernet3.37_vrf_customer37
      redistribute connected
   !
   vrf customer38
      rd 10.1.0.2:38
      route-target import evpn 38:38
      route-target export evpn 38:38
      router-id 10.1.0.2
      neighbor 10.99.0.2 remote-as 65010
      neighbor 10.99.0.2 peer group IPv4-UNDERLAY-PEERS
      neighbor 10.99.0.2 description r1-hub-leaf1_Ethernet3.38_vrf_customer38
      redistribute connected
   !
   vrf customer39
      rd 10.1.0.2:39
      route-target import evpn 39:39
      route-target export evpn 39:39
      router-id 10.1.0.2
      neighbor 10.99.0.2 remote-as 65010
      neighbor 10.99.0.2 peer group IPv4-UNDERLAY-PEERS
      neighbor 10.99.0.2 description r1-hub-leaf1_Ethernet3.39_vrf_customer39
      redistribute connected
   !
   vrf customer40
      rd 10.1.0.2:40
      route-target import evpn 40:40
      route-target export evpn 40:40
      router-id 10.1.0.2
      neighbor 10.99.0.2 remote-as 65010
      neighbor 10.99.0.2 peer group IPv4-UNDERLAY-PEERS
      neighbor 10.99.0.2 description r1-hub-leaf1_Ethernet3.40_vrf_customer40
      redistribute connected
   !
   vrf customer41
      rd 10.1.0.2:41
      route-target import evpn 41:41
      route-target export evpn 41:41
      router-id 10.1.0.2
      neighbor 10.99.0.2 remote-as 65010
      neighbor 10.99.0.2 peer group IPv4-UNDERLAY-PEERS
      neighbor 10.99.0.2 description r1-hub-leaf1_Ethernet3.41_vrf_customer41
      redistribute connected
   !
   vrf customer42
      rd 10.1.0.2:42
      route-target import evpn 42:42
      route-target export evpn 42:42
      router-id 10.1.0.2
      neighbor 10.99.0.2 remote-as 65010
      neighbor 10.99.0.2 peer group IPv4-UNDERLAY-PEERS
      neighbor 10.99.0.2 description r1-hub-leaf1_Ethernet3.42_vrf_customer42
      redistribute connected
   !
   vrf customer43
      rd 10.1.0.2:43
      route-target import evpn 43:43
      route-target export evpn 43:43
      router-id 10.1.0.2
      neighbor 10.99.0.2 remote-as 65010
      neighbor 10.99.0.2 peer group IPv4-UNDERLAY-PEERS
      neighbor 10.99.0.2 description r1-hub-leaf1_Ethernet3.43_vrf_customer43
      redistribute connected
   !
   vrf customer44
      rd 10.1.0.2:44
      route-target import evpn 44:44
      route-target export evpn 44:44
      router-id 10.1.0.2
      neighbor 10.99.0.2 remote-as 65010
      neighbor 10.99.0.2 peer group IPv4-UNDERLAY-PEERS
      neighbor 10.99.0.2 description r1-hub-leaf1_Ethernet3.44_vrf_customer44
      redistribute connected
   !
   vrf customer45
      rd 10.1.0.2:45
      route-target import evpn 45:45
      route-target export evpn 45:45
      router-id 10.1.0.2
      neighbor 10.99.0.2 remote-as 65010
      neighbor 10.99.0.2 peer group IPv4-UNDERLAY-PEERS
      neighbor 10.99.0.2 description r1-hub-leaf1_Ethernet3.45_vrf_customer45
      redistribute connected
   !
   vrf customer46
      rd 10.1.0.2:46
      route-target import evpn 46:46
      route-target export evpn 46:46
      router-id 10.1.0.2
      neighbor 10.99.0.2 remote-as 65010
      neighbor 10.99.0.2 peer group IPv4-UNDERLAY-PEERS
      neighbor 10.99.0.2 description r1-hub-leaf1_Ethernet3.46_vrf_customer46
      redistribute connected
   !
   vrf customer47
      rd 10.1.0.2:47
      route-target import evpn 47:47
      route-target export evpn 47:47
      router-id 10.1.0.2
      neighbor 10.99.0.2 remote-as 65010
      neighbor 10.99.0.2 peer group IPv4-UNDERLAY-PEERS
      neighbor 10.99.0.2 description r1-hub-leaf1_Ethernet3.47_vrf_customer47
      redistribute connected
   !
   vrf customer48
      rd 10.1.0.2:48
      route-target import evpn 48:48
      route-target export evpn 48:48
      router-id 10.1.0.2
      neighbor 10.99.0.2 remote-as 65010
      neighbor 10.99.0.2 peer group IPv4-UNDERLAY-PEERS
      neighbor 10.99.0.2 description r1-hub-leaf1_Ethernet3.48_vrf_customer48
      redistribute connected
   !
   vrf customer49
      rd 10.1.0.2:49
      route-target import evpn 49:49
      route-target export evpn 49:49
      router-id 10.1.0.2
      neighbor 10.99.0.2 remote-as 65010
      neighbor 10.99.0.2 peer group IPv4-UNDERLAY-PEERS
      neighbor 10.99.0.2 description r1-hub-leaf1_Ethernet3.49_vrf_customer49
      redistribute connected
   !
   vrf customer50
      rd 10.1.0.2:50
      route-target import evpn 50:50
      route-target export evpn 50:50
      router-id 10.1.0.2
      neighbor 10.99.0.2 remote-as 65010
      neighbor 10.99.0.2 peer group IPv4-UNDERLAY-PEERS
      neighbor 10.99.0.2 description r1-hub-leaf1_Ethernet3.50_vrf_customer50
      redistribute connected
   !
   vrf customer51
      rd 10.1.0.2:51
      route-target import evpn 51:51
      route-target export evpn 51:51
      router-id 10.1.0.2
      neighbor 10.99.0.2 remote-as 65010
      neighbor 10.99.0.2 peer group IPv4-UNDERLAY-PEERS
      neighbor 10.99.0.2 description r1-hub-leaf1_Ethernet3.51_vrf_customer51
      redistribute connected
   !
   vrf customer52
      rd 10.1.0.2:52
      route-target import evpn 52:52
      route-target export evpn 52:52
      router-id 10.1.0.2
      neighbor 10.99.0.2 remote-as 65010
      neighbor 10.99.0.2 peer group IPv4-UNDERLAY-PEERS
      neighbor 10.99.0.2 description r1-hub-leaf1_Ethernet3.52_vrf_customer52
      redistribute connected
   !
   vrf customer53
      rd 10.1.0.2:53
      route-target import evpn 53:53
      route-target export evpn 53:53
      router-id 10.1.0.2
      neighbor 10.99.0.2 remote-as 65010
      neighbor 10.99.0.2 peer group IPv4-UNDERLAY-PEERS
      neighbor 10.99.0.2 description r1-hub-leaf1_Ethernet3.53_vrf_customer53
      redistribute connected
   !
   vrf customer54
      rd 10.1.0.2:54
      route-target import evpn 54:54
      route-target export evpn 54:54
      router-id 10.1.0.2
      neighbor 10.99.0.2 remote-as 65010
      neighbor 10.99.0.2 peer group IPv4-UNDERLAY-PEERS
      neighbor 10.99.0.2 description r1-hub-leaf1_Ethernet3.54_vrf_customer54
      redistribute connected
   !
   vrf customer55
      rd 10.1.0.2:55
      route-target import evpn 55:55
      route-target export evpn 55:55
      router-id 10.1.0.2
      neighbor 10.99.0.2 remote-as 65010
      neighbor 10.99.0.2 peer group IPv4-UNDERLAY-PEERS
      neighbor 10.99.0.2 description r1-hub-leaf1_Ethernet3.55_vrf_customer55
      redistribute connected
   !
   vrf customer56
      rd 10.1.0.2:56
      route-target import evpn 56:56
      route-target export evpn 56:56
      router-id 10.1.0.2
      neighbor 10.99.0.2 remote-as 65010
      neighbor 10.99.0.2 peer group IPv4-UNDERLAY-PEERS
      neighbor 10.99.0.2 description r1-hub-leaf1_Ethernet3.56_vrf_customer56
      redistribute connected
   !
   vrf customer57
      rd 10.1.0.2:57
      route-target import evpn 57:57
      route-target export evpn 57:57
      router-id 10.1.0.2
      neighbor 10.99.0.2 remote-as 65010
      neighbor 10.99.0.2 peer group IPv4-UNDERLAY-PEERS
      neighbor 10.99.0.2 description r1-hub-leaf1_Ethernet3.57_vrf_customer57
      redistribute connected
   !
   vrf customer58
      rd 10.1.0.2:58
      route-target import evpn 58:58
      route-target export evpn 58:58
      router-id 10.1.0.2
      neighbor 10.99.0.2 remote-as 65010
      neighbor 10.99.0.2 peer group IPv4-UNDERLAY-PEERS
      neighbor 10.99.0.2 description r1-hub-leaf1_Ethernet3.58_vrf_customer58
      redistribute connected
   !
   vrf customer59
      rd 10.1.0.2:59
      route-target import evpn 59:59
      route-target export evpn 59:59
      router-id 10.1.0.2
      neighbor 10.99.0.2 remote-as 65010
      neighbor 10.99.0.2 peer group IPv4-UNDERLAY-PEERS
      neighbor 10.99.0.2 description r1-hub-leaf1_Ethernet3.59_vrf_customer59
      redistribute connected
   !
   vrf customer60
      rd 10.1.0.2:60
      route-target import evpn 60:60
      route-target export evpn 60:60
      router-id 10.1.0.2
      neighbor 10.99.0.2 remote-as 65010
      neighbor 10.99.0.2 peer group IPv4-UNDERLAY-PEERS
      neighbor 10.99.0.2 description r1-hub-leaf1_Ethernet3.60_vrf_customer60
      redistribute connected
   !
   vrf customer61
      rd 10.1.0.2:61
      route-target import evpn 61:61
      route-target export evpn 61:61
      router-id 10.1.0.2
      neighbor 10.99.0.2 remote-as 65010
      neighbor 10.99.0.2 peer group IPv4-UNDERLAY-PEERS
      neighbor 10.99.0.2 description r1-hub-leaf1_Ethernet3.61_vrf_customer61
      redistribute connected
   !
   vrf customer62
      rd 10.1.0.2:62
      route-target import evpn 62:62
      route-target export evpn 62:62
      router-id 10.1.0.2
      neighbor 10.99.0.2 remote-as 65010
      neighbor 10.99.0.2 peer group IPv4-UNDERLAY-PEERS
      neighbor 10.99.0.2 description r1-hub-leaf1_Ethernet3.62_vrf_customer62
      redistribute connected
   !
   vrf customer63
      rd 10.1.0.2:63
      route-target import evpn 63:63
      route-target export evpn 63:63
      router-id 10.1.0.2
      neighbor 10.99.0.2 remote-as 65010
      neighbor 10.99.0.2 peer group IPv4-UNDERLAY-PEERS
      neighbor 10.99.0.2 description r1-hub-leaf1_Ethernet3.63_vrf_customer63
      redistribute connected
   !
   vrf customer64
      rd 10.1.0.2:64
      route-target import evpn 64:64
      route-target export evpn 64:64
      router-id 10.1.0.2
      neighbor 10.99.0.2 remote-as 65010
      neighbor 10.99.0.2 peer group IPv4-UNDERLAY-PEERS
      neighbor 10.99.0.2 description r1-hub-leaf1_Ethernet3.64_vrf_customer64
      redistribute connected
   !
   vrf default
      rd 10.1.0.2:1
      route-target import evpn 1:1
      route-target export evpn 1:1
      route-target export evpn route-map RM-EVPN-EXPORT-VRF-DEFAULT
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

## Filters

### Prefix-lists

#### Prefix-lists Summary

##### PL-LOOPBACKS-EVPN-OVERLAY

| Sequence | Action |
| -------- | ------ |
| 10 | permit 10.1.0.0/26 eq 32 |

#### Prefix-lists Device Configuration

```eos
!
ip prefix-list PL-LOOPBACKS-EVPN-OVERLAY
   seq 10 permit 10.1.0.0/26 eq 32
```

### Route-maps

#### Route-maps Summary

##### RM-BGP-UNDERLAY-PEERS-IN

| Sequence | Type | Match | Set | Sub-Route-Map | Continue |
| -------- | ---- | ----- | --- | ------------- | -------- |
| 40 | permit | - | extcommunity soo 10.1.0.1:111 additive | - | - |

##### RM-BGP-UNDERLAY-PEERS-OUT

| Sequence | Type | Match | Set | Sub-Route-Map | Continue |
| -------- | ---- | ----- | --- | ------------- | -------- |
| 10 | permit | tag 50<br>route-type internal | metric 50 | - | - |
| 20 | permit | - | - | - | - |

##### RM-CONN-2-BGP

| Sequence | Type | Match | Set | Sub-Route-Map | Continue |
| -------- | ---- | ----- | --- | ------------- | -------- |
| 10 | permit | ip address prefix-list PL-LOOPBACKS-EVPN-OVERLAY | extcommunity soo 10.1.0.1:111 additive | - | - |

##### RM-EVPN-EXPORT-VRF-DEFAULT

| Sequence | Type | Match | Set | Sub-Route-Map | Continue |
| -------- | ---- | ----- | --- | ------------- | -------- |
| 10 | permit | extcommunity ECL-EVPN-SOO | - | - | - |

##### RM-EVPN-SOO-IN

| Sequence | Type | Match | Set | Sub-Route-Map | Continue |
| -------- | ---- | ----- | --- | ------------- | -------- |
| 10 | deny | extcommunity ECL-EVPN-SOO | - | - | - |
| 20 | permit | - | - | - | - |

##### RM-EVPN-SOO-OUT

| Sequence | Type | Match | Set | Sub-Route-Map | Continue |
| -------- | ---- | ----- | --- | ------------- | -------- |
| 10 | permit | - | extcommunity soo 10.1.0.1:111 additive | - | - |

##### RM-WAN-HA-PEER-IN

| Sequence | Type | Match | Set | Sub-Route-Map | Continue |
| -------- | ---- | ----- | --- | ------------- | -------- |
| 10 | permit | - | tag 50 | - | - |

##### RM-WAN-HA-PEER-OUT

| Sequence | Type | Match | Set | Sub-Route-Map | Continue |
| -------- | ---- | ----- | --- | ------------- | -------- |
| 10 | permit | route-type internal | local-preference 50 | - | - |
| 20 | permit | - | local-preference 75 | - | - |

#### Route-maps Device Configuration

```eos
!
route-map RM-BGP-UNDERLAY-PEERS-IN permit 40
   description Mark prefixes originated from the LAN
   set extcommunity soo 10.1.0.1:111 additive
!
route-map RM-BGP-UNDERLAY-PEERS-OUT permit 10
   description Make routes learned from WAN HA peer less preferred on LAN routers
   match route-type internal
   match tag 50
   set metric 50
!
route-map RM-BGP-UNDERLAY-PEERS-OUT permit 20
!
route-map RM-CONN-2-BGP permit 10
   match ip address prefix-list PL-LOOPBACKS-EVPN-OVERLAY
   set extcommunity soo 10.1.0.1:111 additive
!
route-map RM-EVPN-EXPORT-VRF-DEFAULT permit 10
   match extcommunity ECL-EVPN-SOO
!
route-map RM-EVPN-SOO-IN deny 10
   match extcommunity ECL-EVPN-SOO
!
route-map RM-EVPN-SOO-IN permit 20
!
route-map RM-EVPN-SOO-OUT permit 10
   set extcommunity soo 10.1.0.1:111 additive
!
route-map RM-WAN-HA-PEER-IN permit 10
   description Set tag 50 on routes received from HA peer over EVPN
   set tag 50
!
route-map RM-WAN-HA-PEER-OUT permit 10
   description Make EVPN routes learned from WAN less preferred on HA peer
   match route-type internal
   set local-preference 50
!
route-map RM-WAN-HA-PEER-OUT permit 20
   description Make locally injected routes less preferred on HA peer
   set local-preference 75
```

### IP Extended Community Lists

#### IP Extended Community Lists Summary

| List Name | Type | Extended Communities |
| --------- | ---- | -------------------- |
| ECL-EVPN-SOO | permit | soo 10.1.0.1:111 |

#### IP Extended Community Lists Device Configuration

```eos
!
ip extcommunity-list ECL-EVPN-SOO permit soo 10.1.0.1:111
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

## Application Traffic Recognition

### Applications

#### IPv4 Applications

| Name | Source Prefix | Destination Prefix | Protocols | Protocol Ranges | TCP Source Port Set | TCP Destination Port Set | UDP Source Port Set | UDP Destination Port Set |
| ---- | ------------- | ------------------ | --------- | --------------- | ------------------- | ------------------------ | ------------------- | ------------------------ |
| APP-CONTROL-PLANE | - | PFX-PATHFINDERS | - | - | - | - | - | - |
| voice-udpport | - | - | udp | - | - | - | - | RTP_PORT |

### Application Profiles

#### Application Profile Name APP-PROFILE-CONTROL-PLANE

| Type | Name | Service |
| ---- | ---- | ------- |
| application | APP-CONTROL-PLANE | - |

#### Application Profile Name VOICE

| Type | Name | Service |
| ---- | ---- | ------- |
| application | voice-udpport | - |

### Field Sets

#### L4 Port Sets

| Name | Ports |
| ---- | ----- |
| RTP_PORT | 1-65000 |

#### IPv4 Prefix Sets

| Name | Prefixes |
| ---- | -------- |
| PFX-PATHFINDERS | 10.255.0.1/32<br>10.255.0.2/32 |

### Router Application-Traffic-Recognition Device Configuration

```eos
!
application traffic recognition
   !
   application ipv4 APP-CONTROL-PLANE
      destination prefix field-set PFX-PATHFINDERS
   !
   application ipv4 voice-udpport
      protocol udp destination port field-set RTP_PORT
   !
   application-profile APP-PROFILE-CONTROL-PLANE
      application APP-CONTROL-PLANE
   !
   application-profile VOICE
      application voice-udpport
   !
   field-set ipv4 prefix PFX-PATHFINDERS
      10.255.0.1/32 10.255.0.2/32
   !
   field-set l4-port RTP_PORT
      1-65000
```

### Router Path-selection

#### TCP MSS Ceiling Configuration

| IPV4 segment size | Direction |
| ----------------- | --------- |
| auto | ingress |

#### Path Groups

##### Path Group gmpls2

| Setting | Value |
| ------  | ----- |
| Path Group ID | 112 |
| IPSec profile | CP-PROFILE |

###### Local Interfaces

| Interface name | Public address | STUN server profile(s) |
| -------------- | -------------- | ---------------------- |
| Ethernet1/9 | - | gmpls2-pf1-Ethernet1_2<br>gmpls2-pf2-Ethernet1_2 |

###### Dynamic Peers Settings

| Setting | Value |
| ------  | ----- |
| IP Local | - |
| IPSec | - |

###### Static Peers

| Router IP | Name | IPv4 address(es) |
| --------- | ---- | ---------------- |
| 10.255.0.1 | pf1 | 172.16.51.6 |
| 10.255.0.2 | pf2 | 172.16.52.6 |

##### Path Group LAN_HA

| Setting | Value |
| ------  | ----- |
| Path Group ID | 65535 |
| IPSec profile | DP-PROFILE |
| Flow assignment | LAN |

###### Local Interfaces

| Interface name | Public address | STUN server profile(s) |
| -------------- | -------------- | ---------------------- |
| Ethernet1/15 | - |  |

###### Static Peers

| Router IP | Name | IPv4 address(es) |
| --------- | ---- | ---------------- |
| 10.1.0.65 | r1-hub-wan1 | 10.10.10.0 |

##### Path Group rmpls4

| Setting | Value |
| ------  | ----- |
| Path Group ID | 104 |
| IPSec profile | CP-PROFILE |

###### Local Interfaces

| Interface name | Public address | STUN server profile(s) |
| -------------- | -------------- | ---------------------- |
| Ethernet1/11 | - | rmpls4-pf1-Ethernet1_8<br>rmpls4-pf2-Ethernet1_8 |

###### Dynamic Peers Settings

| Setting | Value |
| ------  | ----- |
| IP Local | - |
| IPSec | - |

###### Static Peers

| Router IP | Name | IPv4 address(es) |
| --------- | ---- | ---------------- |
| 10.255.0.1 | pf1 | 172.16.51.22 |
| 10.255.0.2 | pf2 | 172.16.52.22 |

#### Load-balance Policies

| Policy Name | Jitter (ms) | Latency (ms) | Loss Rate (%) | Path Groups (priority) | Lowest Hop Count |
| ----------- | ----------- | ------------ | ------------- | ---------------------- | ---------------- |
| LB-DEFAULT-AVT-POLICY-CONTROL-PLANE | - | - | - | gmpls2 (1)<br>LAN_HA (1)<br>rmpls4 (1) | False |
| LB-DEFAULT-AVT-POLICY-DEFAULT | - | - | - | gmpls2 (1)<br>LAN_HA (1)<br>rmpls4 (1) | False |
| LB-TELEPRES-AVT-POLICY-DEFAULT | - | - | - | gmpls2 (1)<br>LAN_HA (1)<br>rmpls4 (1) | False |
| LB-TELEPRES-AVT-POLICY-VOICE | 20 | 120 | 0.3 | gmpls2 (1)<br>LAN_HA (1)<br>rmpls4 (1) | True |

#### Router Path-selection Device Configuration

```eos
!
router path-selection
   tcp mss ceiling ipv4 ingress
   !
   path-group gmpls2 id 112
      ipsec profile CP-PROFILE
      !
      local interface Ethernet1/9
         stun server-profile gmpls2-pf1-Ethernet1_2 gmpls2-pf2-Ethernet1_2
      !
      peer dynamic
      !
      peer static router-ip 10.255.0.1
         name pf1
         ipv4 address 172.16.51.6
      !
      peer static router-ip 10.255.0.2
         name pf2
         ipv4 address 172.16.52.6
   !
   path-group LAN_HA id 65535
      ipsec profile DP-PROFILE
      flow assignment lan
      !
      local interface Ethernet1/15
      !
      peer static router-ip 10.1.0.65
         name r1-hub-wan1
         ipv4 address 10.10.10.0
   !
   path-group rmpls4 id 104
      ipsec profile CP-PROFILE
      !
      local interface Ethernet1/11
         stun server-profile rmpls4-pf1-Ethernet1_8 rmpls4-pf2-Ethernet1_8
      !
      peer dynamic
      !
      peer static router-ip 10.255.0.1
         name pf1
         ipv4 address 172.16.51.22
      !
      peer static router-ip 10.255.0.2
         name pf2
         ipv4 address 172.16.52.22
   !
   load-balance policy LB-DEFAULT-AVT-POLICY-CONTROL-PLANE
      path-group gmpls2
      path-group LAN_HA
      path-group rmpls4
   !
   load-balance policy LB-DEFAULT-AVT-POLICY-DEFAULT
      path-group gmpls2
      path-group LAN_HA
      path-group rmpls4
   !
   load-balance policy LB-TELEPRES-AVT-POLICY-DEFAULT
      path-group gmpls2
      path-group LAN_HA
      path-group rmpls4
   !
   load-balance policy LB-TELEPRES-AVT-POLICY-VOICE
      hop count lowest
      jitter 20
      latency 120
      loss-rate 0.3
      path-group gmpls2
      path-group LAN_HA
      path-group rmpls4
```

## STUN

### STUN Client

#### Server Profiles

| Server Profile | IP address | SSL Profile | Port |
| -------------- | ---------- | ----------- | ---- |
| gmpls2-pf1-Ethernet1_2 | 172.16.51.6 | - | 3478 |
| gmpls2-pf2-Ethernet1_2 | 172.16.52.6 | - | 3478 |
| rmpls4-pf1-Ethernet1_8 | 172.16.51.22 | - | 3478 |
| rmpls4-pf2-Ethernet1_8 | 172.16.52.22 | - | 3478 |

### STUN Device Configuration

```eos
!
stun
   client
      server-profile gmpls2-pf1-Ethernet1_2
         ip address 172.16.51.6
      server-profile gmpls2-pf2-Ethernet1_2
         ip address 172.16.52.6
      server-profile rmpls4-pf1-Ethernet1_8
         ip address 172.16.51.22
      server-profile rmpls4-pf2-Ethernet1_8
         ip address 172.16.52.22
```
