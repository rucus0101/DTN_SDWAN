# r2-site1-wan1

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
| Management1 | oob_management | oob | MGMT | 172.28.139.42/17 | 172.28.128.1 |

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
   ip address 172.28.139.42/17
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
| DP-IKE-POLICY | - | - | - | 10.2.1.65 |
| CP-IKE-POLICY | - | - | - | 10.2.1.65 |

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
      local-id 10.2.1.65
   !
   ike policy CP-IKE-POLICY
      local-id 10.2.1.65
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
| Dps1 | 10.2.1.65/32 | - | 9214 | Hardware: FLOW-TRACKER |  |

#### DPS Interfaces Device Configuration

```eos
!
interface Dps1
   description DPS Interface
   mtu 9214
   flow tracker hardware FLOW-TRACKER
   ip address 10.2.1.65/32
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
| Ethernet6.2 | P2P_LINK_TO_R2-SITE1-LEAF1_Ethernet1.2_vrf_customer2 | l3dot1q | - | 2 |
| Ethernet6.3 | P2P_LINK_TO_R2-SITE1-LEAF1_Ethernet1.3_vrf_customer3 | l3dot1q | - | 3 |
| Ethernet6.4 | P2P_LINK_TO_R2-SITE1-LEAF1_Ethernet1.4_vrf_customer4 | l3dot1q | - | 4 |
| Ethernet6.5 | P2P_LINK_TO_R2-SITE1-LEAF1_Ethernet1.5_vrf_customer5 | l3dot1q | - | 5 |
| Ethernet6.6 | P2P_LINK_TO_R2-SITE1-LEAF1_Ethernet1.6_vrf_customer6 | l3dot1q | - | 6 |
| Ethernet6.7 | P2P_LINK_TO_R2-SITE1-LEAF1_Ethernet1.7_vrf_customer7 | l3dot1q | - | 7 |
| Ethernet6.8 | P2P_LINK_TO_R2-SITE1-LEAF1_Ethernet1.8_vrf_customer8 | l3dot1q | - | 8 |
| Ethernet6.9 | P2P_LINK_TO_R2-SITE1-LEAF1_Ethernet1.9_vrf_customer9 | l3dot1q | - | 9 |
| Ethernet6.10 | P2P_LINK_TO_R2-SITE1-LEAF1_Ethernet1.10_vrf_customer10 | l3dot1q | - | 10 |
| Ethernet6.11 | P2P_LINK_TO_R2-SITE1-LEAF1_Ethernet1.11_vrf_customer11 | l3dot1q | - | 11 |
| Ethernet6.12 | P2P_LINK_TO_R2-SITE1-LEAF1_Ethernet1.12_vrf_customer12 | l3dot1q | - | 12 |
| Ethernet6.13 | P2P_LINK_TO_R2-SITE1-LEAF1_Ethernet1.13_vrf_customer13 | l3dot1q | - | 13 |
| Ethernet6.14 | P2P_LINK_TO_R2-SITE1-LEAF1_Ethernet1.14_vrf_customer14 | l3dot1q | - | 14 |
| Ethernet6.254 | P2P_LINK_TO_R2-SITE1-LEAF1_Ethernet1.254_vrf_customer1 | l3dot1q | - | 254 |

##### IPv4

| Interface | Description | Type | Channel Group | IP Address | VRF |  MTU | Shutdown | ACL In | ACL Out |
| --------- | ----------- | -----| ------------- | ---------- | ----| ---- | -------- | ------ | ------- |
| Ethernet1 | rmpls1_R2-SITE1-MPLS1 | routed | - | 172.16.211.2/30 | default | - | False | - | - |
| Ethernet6 | P2P_LINK_TO_R2-SITE1-LEAF1_Ethernet1 | routed | - | 10.2.1.129/31 | default | 9214 | False | - | - |
| Ethernet6.2 | P2P_LINK_TO_R2-SITE1-LEAF1_Ethernet1.2_vrf_customer2 | l3dot1q | - | 10.2.1.129/31 | customer2 | 9214 | False | - | - |
| Ethernet6.3 | P2P_LINK_TO_R2-SITE1-LEAF1_Ethernet1.3_vrf_customer3 | l3dot1q | - | 10.2.1.129/31 | customer3 | 9214 | False | - | - |
| Ethernet6.4 | P2P_LINK_TO_R2-SITE1-LEAF1_Ethernet1.4_vrf_customer4 | l3dot1q | - | 10.2.1.129/31 | customer4 | 9214 | False | - | - |
| Ethernet6.5 | P2P_LINK_TO_R2-SITE1-LEAF1_Ethernet1.5_vrf_customer5 | l3dot1q | - | 10.2.1.129/31 | customer5 | 9214 | False | - | - |
| Ethernet6.6 | P2P_LINK_TO_R2-SITE1-LEAF1_Ethernet1.6_vrf_customer6 | l3dot1q | - | 10.2.1.129/31 | customer6 | 9214 | False | - | - |
| Ethernet6.7 | P2P_LINK_TO_R2-SITE1-LEAF1_Ethernet1.7_vrf_customer7 | l3dot1q | - | 10.2.1.129/31 | customer7 | 9214 | False | - | - |
| Ethernet6.8 | P2P_LINK_TO_R2-SITE1-LEAF1_Ethernet1.8_vrf_customer8 | l3dot1q | - | 10.2.1.129/31 | customer8 | 9214 | False | - | - |
| Ethernet6.9 | P2P_LINK_TO_R2-SITE1-LEAF1_Ethernet1.9_vrf_customer9 | l3dot1q | - | 10.2.1.129/31 | customer9 | 9214 | False | - | - |
| Ethernet6.10 | P2P_LINK_TO_R2-SITE1-LEAF1_Ethernet1.10_vrf_customer10 | l3dot1q | - | 10.2.1.129/31 | customer10 | 9214 | False | - | - |
| Ethernet6.11 | P2P_LINK_TO_R2-SITE1-LEAF1_Ethernet1.11_vrf_customer11 | l3dot1q | - | 10.2.1.129/31 | customer11 | 9214 | False | - | - |
| Ethernet6.12 | P2P_LINK_TO_R2-SITE1-LEAF1_Ethernet1.12_vrf_customer12 | l3dot1q | - | 10.2.1.129/31 | customer12 | 9214 | False | - | - |
| Ethernet6.13 | P2P_LINK_TO_R2-SITE1-LEAF1_Ethernet1.13_vrf_customer13 | l3dot1q | - | 10.2.1.129/31 | customer13 | 9214 | False | - | - |
| Ethernet6.14 | P2P_LINK_TO_R2-SITE1-LEAF1_Ethernet1.14_vrf_customer14 | l3dot1q | - | 10.2.1.129/31 | customer14 | 9214 | False | - | - |
| Ethernet6.254 | P2P_LINK_TO_R2-SITE1-LEAF1_Ethernet1.254_vrf_customer1 | l3dot1q | - | 10.2.1.129/31 | customer1 | 9214 | False | - | - |
| Ethernet7 | DIRECT LAN HA LINK | routed | - | 10.10.10.0/31 | default | - | False | - | - |

#### Ethernet Interfaces Device Configuration

```eos
!
interface Ethernet1
   description rmpls1_R2-SITE1-MPLS1
   no shutdown
   no switchport
   ip address 172.16.211.2/30
!
interface Ethernet6
   description P2P_LINK_TO_R2-SITE1-LEAF1_Ethernet1
   no shutdown
   mtu 9214
   no switchport
   ip address 10.2.1.129/31
!
interface Ethernet6.2
   description P2P_LINK_TO_R2-SITE1-LEAF1_Ethernet1.2_vrf_customer2
   no shutdown
   mtu 9214
   encapsulation dot1q vlan 2
   vrf customer2
   ip address 10.2.1.129/31
!
interface Ethernet6.3
   description P2P_LINK_TO_R2-SITE1-LEAF1_Ethernet1.3_vrf_customer3
   no shutdown
   mtu 9214
   encapsulation dot1q vlan 3
   vrf customer3
   ip address 10.2.1.129/31
!
interface Ethernet6.4
   description P2P_LINK_TO_R2-SITE1-LEAF1_Ethernet1.4_vrf_customer4
   no shutdown
   mtu 9214
   encapsulation dot1q vlan 4
   vrf customer4
   ip address 10.2.1.129/31
!
interface Ethernet6.5
   description P2P_LINK_TO_R2-SITE1-LEAF1_Ethernet1.5_vrf_customer5
   no shutdown
   mtu 9214
   encapsulation dot1q vlan 5
   vrf customer5
   ip address 10.2.1.129/31
!
interface Ethernet6.6
   description P2P_LINK_TO_R2-SITE1-LEAF1_Ethernet1.6_vrf_customer6
   no shutdown
   mtu 9214
   encapsulation dot1q vlan 6
   vrf customer6
   ip address 10.2.1.129/31
!
interface Ethernet6.7
   description P2P_LINK_TO_R2-SITE1-LEAF1_Ethernet1.7_vrf_customer7
   no shutdown
   mtu 9214
   encapsulation dot1q vlan 7
   vrf customer7
   ip address 10.2.1.129/31
!
interface Ethernet6.8
   description P2P_LINK_TO_R2-SITE1-LEAF1_Ethernet1.8_vrf_customer8
   no shutdown
   mtu 9214
   encapsulation dot1q vlan 8
   vrf customer8
   ip address 10.2.1.129/31
!
interface Ethernet6.9
   description P2P_LINK_TO_R2-SITE1-LEAF1_Ethernet1.9_vrf_customer9
   no shutdown
   mtu 9214
   encapsulation dot1q vlan 9
   vrf customer9
   ip address 10.2.1.129/31
!
interface Ethernet6.10
   description P2P_LINK_TO_R2-SITE1-LEAF1_Ethernet1.10_vrf_customer10
   no shutdown
   mtu 9214
   encapsulation dot1q vlan 10
   vrf customer10
   ip address 10.2.1.129/31
!
interface Ethernet6.11
   description P2P_LINK_TO_R2-SITE1-LEAF1_Ethernet1.11_vrf_customer11
   no shutdown
   mtu 9214
   encapsulation dot1q vlan 11
   vrf customer11
   ip address 10.2.1.129/31
!
interface Ethernet6.12
   description P2P_LINK_TO_R2-SITE1-LEAF1_Ethernet1.12_vrf_customer12
   no shutdown
   mtu 9214
   encapsulation dot1q vlan 12
   vrf customer12
   ip address 10.2.1.129/31
!
interface Ethernet6.13
   description P2P_LINK_TO_R2-SITE1-LEAF1_Ethernet1.13_vrf_customer13
   no shutdown
   mtu 9214
   encapsulation dot1q vlan 13
   vrf customer13
   ip address 10.2.1.129/31
!
interface Ethernet6.14
   description P2P_LINK_TO_R2-SITE1-LEAF1_Ethernet1.14_vrf_customer14
   no shutdown
   mtu 9214
   encapsulation dot1q vlan 14
   vrf customer14
   ip address 10.2.1.129/31
!
interface Ethernet6.254
   description P2P_LINK_TO_R2-SITE1-LEAF1_Ethernet1.254_vrf_customer1
   no shutdown
   mtu 9214
   encapsulation dot1q vlan 254
   vrf customer1
   ip address 10.2.1.129/31
!
interface Ethernet7
   description DIRECT LAN HA LINK
   no shutdown
   no switchport
   ip address 10.10.10.0/31
```

### Loopback Interfaces

#### Loopback Interfaces Summary

##### IPv4

| Interface | Description | VRF | IP Address |
| --------- | ----------- | --- | ---------- |
| Loopback0 | Router_ID | default | 10.2.1.1/32 |

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
   ip address 10.2.1.1/32
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
| default | 1 | - |

#### VXLAN Interface Device Configuration

```eos
!
interface Vxlan1
   description r2-site1-wan1_VTEP
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
| MGMT | false |

### Static Routes

#### Static Routes Summary

| VRF | Destination Prefix | Next Hop IP | Exit interface | Administrative Distance | Tag | Route Name | Metric |
| --- | ------------------ | ----------- | -------------- | ----------------------- | --- | ---------- | ------ |
| MGMT | 0.0.0.0/0 | 172.28.128.1 | - | 1 | - | - | - |
| default | 172.16.0.0/16 | 172.16.211.1 | - | 1 | - | - | - |

#### Static Routes Device Configuration

```eos
!
ip route vrf MGMT 0.0.0.0/0 172.28.128.1
ip route 172.16.0.0/16 172.16.211.1
```

### Router Adaptive Virtual Topology

#### Router Adaptive Virtual Topology Summary

Topology role: edge

| Hierarchy | Name | ID |
| --------- | ---- | -- |
| Region | Region2 | 2 |
| Zone | Region2-ZONE | 1 |
| Site | REGION2_SITE1 | 103 |

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
   topology role edge
   region Region2 id 2
   zone Region2-ZONE id 1
   site REGION2_SITE1 id 103
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
| 65000 | 10.2.1.1 |

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
| 10.2.1.66 | 65000 | default | - | all | - | - | - | - | True | - | - |
| 10.2.1.128 | 65021 | default | - | Inherited from peer group IPv4-UNDERLAY-PEERS | Inherited from peer group IPv4-UNDERLAY-PEERS | - | - | - | - | - | - |
| 10.255.0.1 | Inherited from peer group WAN-OVERLAY-PEERS | default | - | Inherited from peer group WAN-OVERLAY-PEERS | Inherited from peer group WAN-OVERLAY-PEERS | - | Inherited from peer group WAN-OVERLAY-PEERS(interval: 1000, min_rx: 1000, multiplier: 10) | - | - | - | Inherited from peer group WAN-OVERLAY-PEERS |
| 10.255.0.2 | Inherited from peer group WAN-OVERLAY-PEERS | default | - | Inherited from peer group WAN-OVERLAY-PEERS | Inherited from peer group WAN-OVERLAY-PEERS | - | Inherited from peer group WAN-OVERLAY-PEERS(interval: 1000, min_rx: 1000, multiplier: 10) | - | - | - | Inherited from peer group WAN-OVERLAY-PEERS |
| 10.2.1.128 | 65021 | customer1 | - | Inherited from peer group IPv4-UNDERLAY-PEERS | Inherited from peer group IPv4-UNDERLAY-PEERS | - | - | - | - | - | - |
| 10.2.1.128 | 65021 | customer2 | - | Inherited from peer group IPv4-UNDERLAY-PEERS | Inherited from peer group IPv4-UNDERLAY-PEERS | - | - | - | - | - | - |
| 10.2.1.128 | 65021 | customer3 | - | Inherited from peer group IPv4-UNDERLAY-PEERS | Inherited from peer group IPv4-UNDERLAY-PEERS | - | - | - | - | - | - |
| 10.2.1.128 | 65021 | customer4 | - | Inherited from peer group IPv4-UNDERLAY-PEERS | Inherited from peer group IPv4-UNDERLAY-PEERS | - | - | - | - | - | - |
| 10.2.1.128 | 65021 | customer5 | - | Inherited from peer group IPv4-UNDERLAY-PEERS | Inherited from peer group IPv4-UNDERLAY-PEERS | - | - | - | - | - | - |
| 10.2.1.128 | 65021 | customer6 | - | Inherited from peer group IPv4-UNDERLAY-PEERS | Inherited from peer group IPv4-UNDERLAY-PEERS | - | - | - | - | - | - |
| 10.2.1.128 | 65021 | customer7 | - | Inherited from peer group IPv4-UNDERLAY-PEERS | Inherited from peer group IPv4-UNDERLAY-PEERS | - | - | - | - | - | - |
| 10.2.1.128 | 65021 | customer8 | - | Inherited from peer group IPv4-UNDERLAY-PEERS | Inherited from peer group IPv4-UNDERLAY-PEERS | - | - | - | - | - | - |
| 10.2.1.128 | 65021 | customer9 | - | Inherited from peer group IPv4-UNDERLAY-PEERS | Inherited from peer group IPv4-UNDERLAY-PEERS | - | - | - | - | - | - |
| 10.2.1.128 | 65021 | customer10 | - | Inherited from peer group IPv4-UNDERLAY-PEERS | Inherited from peer group IPv4-UNDERLAY-PEERS | - | - | - | - | - | - |
| 10.2.1.128 | 65021 | customer11 | - | Inherited from peer group IPv4-UNDERLAY-PEERS | Inherited from peer group IPv4-UNDERLAY-PEERS | - | - | - | - | - | - |
| 10.2.1.128 | 65021 | customer12 | - | Inherited from peer group IPv4-UNDERLAY-PEERS | Inherited from peer group IPv4-UNDERLAY-PEERS | - | - | - | - | - | - |
| 10.2.1.128 | 65021 | customer13 | - | Inherited from peer group IPv4-UNDERLAY-PEERS | Inherited from peer group IPv4-UNDERLAY-PEERS | - | - | - | - | - | - |
| 10.2.1.128 | 65021 | customer14 | - | Inherited from peer group IPv4-UNDERLAY-PEERS | Inherited from peer group IPv4-UNDERLAY-PEERS | - | - | - | - | - | - |

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
| customer1 | 10.2.1.1:254 | connected |
| customer2 | 10.2.1.1:2 | connected |
| customer3 | 10.2.1.1:3 | connected |
| customer4 | 10.2.1.1:4 | connected |
| customer5 | 10.2.1.1:5 | connected |
| customer6 | 10.2.1.1:6 | connected |
| customer7 | 10.2.1.1:7 | connected |
| customer8 | 10.2.1.1:8 | connected |
| customer9 | 10.2.1.1:9 | connected |
| customer10 | 10.2.1.1:10 | connected |
| customer11 | 10.2.1.1:11 | connected |
| customer12 | 10.2.1.1:12 | connected |
| customer13 | 10.2.1.1:13 | connected |
| customer14 | 10.2.1.1:14 | connected |
| default | 10.2.1.1:1 | - |

#### Router BGP Device Configuration

```eos
!
router bgp 65000
   router-id 10.2.1.1
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
   neighbor 10.2.1.66 remote-as 65000
   neighbor 10.2.1.66 description r2-site1-wan2
   neighbor 10.2.1.66 route-reflector-client
   neighbor 10.2.1.66 update-source Dps1
   neighbor 10.2.1.66 route-map RM-WAN-HA-PEER-IN in
   neighbor 10.2.1.66 route-map RM-WAN-HA-PEER-OUT out
   neighbor 10.2.1.66 send-community
   neighbor 10.2.1.128 peer group IPv4-UNDERLAY-PEERS
   neighbor 10.2.1.128 remote-as 65021
   neighbor 10.2.1.128 description r2-site1-leaf1_Ethernet1
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
      neighbor 10.2.1.66 activate
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
      rd 10.2.1.1:254
      route-target import evpn 254:254
      route-target export evpn 254:254
      router-id 10.2.1.1
      neighbor 10.2.1.128 remote-as 65021
      neighbor 10.2.1.128 peer group IPv4-UNDERLAY-PEERS
      neighbor 10.2.1.128 description r2-site1-leaf1_Ethernet1.254_vrf_customer1
      redistribute connected
   !
   vrf customer2
      rd 10.2.1.1:2
      route-target import evpn 2:2
      route-target export evpn 2:2
      router-id 10.2.1.1
      neighbor 10.2.1.128 remote-as 65021
      neighbor 10.2.1.128 peer group IPv4-UNDERLAY-PEERS
      neighbor 10.2.1.128 description r2-site1-leaf1_Ethernet1.2_vrf_customer2
      redistribute connected
   !
   vrf customer3
      rd 10.2.1.1:3
      route-target import evpn 3:3
      route-target export evpn 3:3
      router-id 10.2.1.1
      neighbor 10.2.1.128 remote-as 65021
      neighbor 10.2.1.128 peer group IPv4-UNDERLAY-PEERS
      neighbor 10.2.1.128 description r2-site1-leaf1_Ethernet1.3_vrf_customer3
      redistribute connected
   !
   vrf customer4
      rd 10.2.1.1:4
      route-target import evpn 4:4
      route-target export evpn 4:4
      router-id 10.2.1.1
      neighbor 10.2.1.128 remote-as 65021
      neighbor 10.2.1.128 peer group IPv4-UNDERLAY-PEERS
      neighbor 10.2.1.128 description r2-site1-leaf1_Ethernet1.4_vrf_customer4
      redistribute connected
   !
   vrf customer5
      rd 10.2.1.1:5
      route-target import evpn 5:5
      route-target export evpn 5:5
      router-id 10.2.1.1
      neighbor 10.2.1.128 remote-as 65021
      neighbor 10.2.1.128 peer group IPv4-UNDERLAY-PEERS
      neighbor 10.2.1.128 description r2-site1-leaf1_Ethernet1.5_vrf_customer5
      redistribute connected
   !
   vrf customer6
      rd 10.2.1.1:6
      route-target import evpn 6:6
      route-target export evpn 6:6
      router-id 10.2.1.1
      neighbor 10.2.1.128 remote-as 65021
      neighbor 10.2.1.128 peer group IPv4-UNDERLAY-PEERS
      neighbor 10.2.1.128 description r2-site1-leaf1_Ethernet1.6_vrf_customer6
      redistribute connected
   !
   vrf customer7
      rd 10.2.1.1:7
      route-target import evpn 7:7
      route-target export evpn 7:7
      router-id 10.2.1.1
      neighbor 10.2.1.128 remote-as 65021
      neighbor 10.2.1.128 peer group IPv4-UNDERLAY-PEERS
      neighbor 10.2.1.128 description r2-site1-leaf1_Ethernet1.7_vrf_customer7
      redistribute connected
   !
   vrf customer8
      rd 10.2.1.1:8
      route-target import evpn 8:8
      route-target export evpn 8:8
      router-id 10.2.1.1
      neighbor 10.2.1.128 remote-as 65021
      neighbor 10.2.1.128 peer group IPv4-UNDERLAY-PEERS
      neighbor 10.2.1.128 description r2-site1-leaf1_Ethernet1.8_vrf_customer8
      redistribute connected
   !
   vrf customer9
      rd 10.2.1.1:9
      route-target import evpn 9:9
      route-target export evpn 9:9
      router-id 10.2.1.1
      neighbor 10.2.1.128 remote-as 65021
      neighbor 10.2.1.128 peer group IPv4-UNDERLAY-PEERS
      neighbor 10.2.1.128 description r2-site1-leaf1_Ethernet1.9_vrf_customer9
      redistribute connected
   !
   vrf customer10
      rd 10.2.1.1:10
      route-target import evpn 10:10
      route-target export evpn 10:10
      router-id 10.2.1.1
      neighbor 10.2.1.128 remote-as 65021
      neighbor 10.2.1.128 peer group IPv4-UNDERLAY-PEERS
      neighbor 10.2.1.128 description r2-site1-leaf1_Ethernet1.10_vrf_customer10
      redistribute connected
   !
   vrf customer11
      rd 10.2.1.1:11
      route-target import evpn 11:11
      route-target export evpn 11:11
      router-id 10.2.1.1
      neighbor 10.2.1.128 remote-as 65021
      neighbor 10.2.1.128 peer group IPv4-UNDERLAY-PEERS
      neighbor 10.2.1.128 description r2-site1-leaf1_Ethernet1.11_vrf_customer11
      redistribute connected
   !
   vrf customer12
      rd 10.2.1.1:12
      route-target import evpn 12:12
      route-target export evpn 12:12
      router-id 10.2.1.1
      neighbor 10.2.1.128 remote-as 65021
      neighbor 10.2.1.128 peer group IPv4-UNDERLAY-PEERS
      neighbor 10.2.1.128 description r2-site1-leaf1_Ethernet1.12_vrf_customer12
      redistribute connected
   !
   vrf customer13
      rd 10.2.1.1:13
      route-target import evpn 13:13
      route-target export evpn 13:13
      router-id 10.2.1.1
      neighbor 10.2.1.128 remote-as 65021
      neighbor 10.2.1.128 peer group IPv4-UNDERLAY-PEERS
      neighbor 10.2.1.128 description r2-site1-leaf1_Ethernet1.13_vrf_customer13
      redistribute connected
   !
   vrf customer14
      rd 10.2.1.1:14
      route-target import evpn 14:14
      route-target export evpn 14:14
      router-id 10.2.1.1
      neighbor 10.2.1.128 remote-as 65021
      neighbor 10.2.1.128 peer group IPv4-UNDERLAY-PEERS
      neighbor 10.2.1.128 description r2-site1-leaf1_Ethernet1.14_vrf_customer14
      redistribute connected
   !
   vrf default
      rd 10.2.1.1:1
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
| 10 | permit 10.2.1.0/26 eq 32 |

#### Prefix-lists Device Configuration

```eos
!
ip prefix-list PL-LOOPBACKS-EVPN-OVERLAY
   seq 10 permit 10.2.1.0/26 eq 32
```

### Route-maps

#### Route-maps Summary

##### RM-BGP-UNDERLAY-PEERS-IN

| Sequence | Type | Match | Set | Sub-Route-Map | Continue |
| -------- | ---- | ----- | --- | ------------- | -------- |
| 40 | permit | - | extcommunity soo 10.2.1.1:103 additive | - | - |

##### RM-BGP-UNDERLAY-PEERS-OUT

| Sequence | Type | Match | Set | Sub-Route-Map | Continue |
| -------- | ---- | ----- | --- | ------------- | -------- |
| 10 | permit | tag 50<br>route-type internal | metric 50 | - | - |
| 20 | permit | - | - | - | - |

##### RM-CONN-2-BGP

| Sequence | Type | Match | Set | Sub-Route-Map | Continue |
| -------- | ---- | ----- | --- | ------------- | -------- |
| 10 | permit | ip address prefix-list PL-LOOPBACKS-EVPN-OVERLAY | extcommunity soo 10.2.1.1:103 additive | - | - |

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
| 10 | permit | - | extcommunity soo 10.2.1.1:103 additive | - | - |

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
   set extcommunity soo 10.2.1.1:103 additive
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
   set extcommunity soo 10.2.1.1:103 additive
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
   set extcommunity soo 10.2.1.1:103 additive
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
| ECL-EVPN-SOO | permit | soo 10.2.1.1:103 |

#### IP Extended Community Lists Device Configuration

```eos
!
ip extcommunity-list ECL-EVPN-SOO permit soo 10.2.1.1:103
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

##### Path Group LAN_HA

| Setting | Value |
| ------  | ----- |
| Path Group ID | 65535 |
| IPSec profile | DP-PROFILE |
| Flow assignment | LAN |

###### Local Interfaces

| Interface name | Public address | STUN server profile(s) |
| -------------- | -------------- | ---------------------- |
| Ethernet7 | - |  |

###### Static Peers

| Router IP | Name | IPv4 address(es) |
| --------- | ---- | ---------------- |
| 10.2.1.66 | r2-site1-wan2 | 10.10.10.1 |

##### Path Group rmpls1

| Setting | Value |
| ------  | ----- |
| Path Group ID | 101 |
| IPSec profile | CP-PROFILE |

###### Local Interfaces

| Interface name | Public address | STUN server profile(s) |
| -------------- | -------------- | ---------------------- |
| Ethernet1 | - | rmpls1-pf1-Ethernet1_5<br>rmpls1-pf2-Ethernet1_5 |

###### Dynamic Peers Settings

| Setting | Value |
| ------  | ----- |
| IP Local | - |
| IPSec | - |

###### Static Peers

| Router IP | Name | IPv4 address(es) |
| --------- | ---- | ---------------- |
| 10.255.0.1 | pf1 | 172.16.51.10 |
| 10.255.0.2 | pf2 | 172.16.52.10 |

#### Load-balance Policies

| Policy Name | Jitter (ms) | Latency (ms) | Loss Rate (%) | Path Groups (priority) | Lowest Hop Count |
| ----------- | ----------- | ------------ | ------------- | ---------------------- | ---------------- |
| LB-DEFAULT-AVT-POLICY-CONTROL-PLANE | - | - | - | LAN_HA (1)<br>rmpls1 (1) | False |
| LB-DEFAULT-AVT-POLICY-DEFAULT | - | - | - | LAN_HA (1)<br>rmpls1 (1) | False |
| LB-TELEPRES-AVT-POLICY-DEFAULT | - | - | - | LAN_HA (1)<br>rmpls1 (1) | False |
| LB-TELEPRES-AVT-POLICY-VOICE | 20 | 120 | 0.3 | LAN_HA (1)<br>rmpls1 (1) | True |

#### Router Path-selection Device Configuration

```eos
!
router path-selection
   tcp mss ceiling ipv4 ingress
   !
   path-group LAN_HA id 65535
      ipsec profile DP-PROFILE
      flow assignment lan
      !
      local interface Ethernet7
      !
      peer static router-ip 10.2.1.66
         name r2-site1-wan2
         ipv4 address 10.10.10.1
   !
   path-group rmpls1 id 101
      ipsec profile CP-PROFILE
      !
      local interface Ethernet1
         stun server-profile rmpls1-pf1-Ethernet1_5 rmpls1-pf2-Ethernet1_5
      !
      peer dynamic
      !
      peer static router-ip 10.255.0.1
         name pf1
         ipv4 address 172.16.51.10
      !
      peer static router-ip 10.255.0.2
         name pf2
         ipv4 address 172.16.52.10
   !
   load-balance policy LB-DEFAULT-AVT-POLICY-CONTROL-PLANE
      path-group LAN_HA
      path-group rmpls1
   !
   load-balance policy LB-DEFAULT-AVT-POLICY-DEFAULT
      path-group LAN_HA
      path-group rmpls1
   !
   load-balance policy LB-TELEPRES-AVT-POLICY-DEFAULT
      path-group LAN_HA
      path-group rmpls1
   !
   load-balance policy LB-TELEPRES-AVT-POLICY-VOICE
      hop count lowest
      jitter 20
      latency 120
      loss-rate 0.3
      path-group LAN_HA
      path-group rmpls1
```

## STUN

### STUN Client

#### Server Profiles

| Server Profile | IP address | SSL Profile | Port |
| -------------- | ---------- | ----------- | ---- |
| rmpls1-pf1-Ethernet1_5 | 172.16.51.10 | - | 3478 |
| rmpls1-pf2-Ethernet1_5 | 172.16.52.10 | - | 3478 |

### STUN Device Configuration

```eos
!
stun
   client
      server-profile rmpls1-pf1-Ethernet1_5
         ip address 172.16.51.10
      server-profile rmpls1-pf2-Ethernet1_5
         ip address 172.16.52.10
```