# r2-site2-wan2

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
| Management1 | oob_management | oob | MGMT | 172.28.143.227/17 | 172.28.128.1 |

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
   ip address 172.28.143.227/17
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
| DP-IKE-POLICY | - | - | - | 10.2.2.66 |
| CP-IKE-POLICY | - | - | - | 10.2.2.66 |

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
      local-id 10.2.2.66
   !
   ike policy CP-IKE-POLICY
      local-id 10.2.2.66
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
| Dps1 | 10.2.2.66/32 | - | 9214 | Hardware: FLOW-TRACKER |  |

#### DPS Interfaces Device Configuration

```eos
!
interface Dps1
   description DPS Interface
   mtu 9214
   flow tracker hardware FLOW-TRACKER
   ip address 10.2.2.66/32
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
| Ethernet2.2 | P2P_LINK_TO_R2-SITE2-LEAF1_Ethernet48.2_vrf_customer2 | l3dot1q | - | 2 |
| Ethernet2.3 | P2P_LINK_TO_R2-SITE2-LEAF1_Ethernet48.3_vrf_customer3 | l3dot1q | - | 3 |
| Ethernet2.4 | P2P_LINK_TO_R2-SITE2-LEAF1_Ethernet48.4_vrf_customer4 | l3dot1q | - | 4 |
| Ethernet2.5 | P2P_LINK_TO_R2-SITE2-LEAF1_Ethernet48.5_vrf_customer5 | l3dot1q | - | 5 |
| Ethernet2.6 | P2P_LINK_TO_R2-SITE2-LEAF1_Ethernet48.6_vrf_customer6 | l3dot1q | - | 6 |
| Ethernet2.254 | P2P_LINK_TO_R2-SITE2-LEAF1_Ethernet48.254_vrf_customer1 | l3dot1q | - | 254 |

##### IPv4

| Interface | Description | Type | Channel Group | IP Address | VRF |  MTU | Shutdown | ACL In | ACL Out |
| --------- | ----------- | -----| ------------- | ---------- | ----| ---- | -------- | ------ | ------- |
| Ethernet1 | rmpls2_R2-SITE2-MPLS2 | routed | - | 172.16.222.2/30 | default | - | False | - | - |
| Ethernet2 | P2P_LINK_TO_R2-SITE2-LEAF1_Ethernet48 | routed | - | 10.2.2.131/31 | default | 9214 | False | - | - |
| Ethernet2.2 | P2P_LINK_TO_R2-SITE2-LEAF1_Ethernet48.2_vrf_customer2 | l3dot1q | - | 10.2.2.131/31 | customer2 | 9214 | False | - | - |
| Ethernet2.3 | P2P_LINK_TO_R2-SITE2-LEAF1_Ethernet48.3_vrf_customer3 | l3dot1q | - | 10.2.2.131/31 | customer3 | 9214 | False | - | - |
| Ethernet2.4 | P2P_LINK_TO_R2-SITE2-LEAF1_Ethernet48.4_vrf_customer4 | l3dot1q | - | 10.2.2.131/31 | customer4 | 9214 | False | - | - |
| Ethernet2.5 | P2P_LINK_TO_R2-SITE2-LEAF1_Ethernet48.5_vrf_customer5 | l3dot1q | - | 10.2.2.131/31 | customer5 | 9214 | False | - | - |
| Ethernet2.6 | P2P_LINK_TO_R2-SITE2-LEAF1_Ethernet48.6_vrf_customer6 | l3dot1q | - | 10.2.2.131/31 | customer6 | 9214 | False | - | - |
| Ethernet2.254 | P2P_LINK_TO_R2-SITE2-LEAF1_Ethernet48.254_vrf_customer1 | l3dot1q | - | 10.2.2.131/31 | customer1 | 9214 | False | - | - |
| Ethernet3 | DIRECT LAN HA LINK | routed | - | 10.10.10.1/31 | default | - | False | - | - |

#### Ethernet Interfaces Device Configuration

```eos
!
interface Ethernet1
   description rmpls2_R2-SITE2-MPLS2
   no shutdown
   no switchport
   ip address 172.16.222.2/30
!
interface Ethernet2
   description P2P_LINK_TO_R2-SITE2-LEAF1_Ethernet48
   no shutdown
   mtu 9214
   no switchport
   ip address 10.2.2.131/31
!
interface Ethernet2.2
   description P2P_LINK_TO_R2-SITE2-LEAF1_Ethernet48.2_vrf_customer2
   no shutdown
   mtu 9214
   encapsulation dot1q vlan 2
   vrf customer2
   ip address 10.2.2.131/31
!
interface Ethernet2.3
   description P2P_LINK_TO_R2-SITE2-LEAF1_Ethernet48.3_vrf_customer3
   no shutdown
   mtu 9214
   encapsulation dot1q vlan 3
   vrf customer3
   ip address 10.2.2.131/31
!
interface Ethernet2.4
   description P2P_LINK_TO_R2-SITE2-LEAF1_Ethernet48.4_vrf_customer4
   no shutdown
   mtu 9214
   encapsulation dot1q vlan 4
   vrf customer4
   ip address 10.2.2.131/31
!
interface Ethernet2.5
   description P2P_LINK_TO_R2-SITE2-LEAF1_Ethernet48.5_vrf_customer5
   no shutdown
   mtu 9214
   encapsulation dot1q vlan 5
   vrf customer5
   ip address 10.2.2.131/31
!
interface Ethernet2.6
   description P2P_LINK_TO_R2-SITE2-LEAF1_Ethernet48.6_vrf_customer6
   no shutdown
   mtu 9214
   encapsulation dot1q vlan 6
   vrf customer6
   ip address 10.2.2.131/31
!
interface Ethernet2.254
   description P2P_LINK_TO_R2-SITE2-LEAF1_Ethernet48.254_vrf_customer1
   no shutdown
   mtu 9214
   encapsulation dot1q vlan 254
   vrf customer1
   ip address 10.2.2.131/31
!
interface Ethernet3
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
| Loopback0 | Router_ID | default | 10.2.2.2/32 |

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
   ip address 10.2.2.2/32
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
| default | 1 | - |

#### VXLAN Interface Device Configuration

```eos
!
interface Vxlan1
   description r2-site2-wan2_VTEP
   vxlan source-interface Dps1
   vxlan udp-port 4789
   vxlan vrf customer1 vni 254
   vxlan vrf customer2 vni 2
   vxlan vrf customer3 vni 3
   vxlan vrf customer4 vni 4
   vxlan vrf customer5 vni 5
   vxlan vrf customer6 vni 6
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
| default | 172.16.0.0/16 | 172.16.222.1 | - | 1 | - | - | - |

#### Static Routes Device Configuration

```eos
!
ip route vrf MGMT 0.0.0.0/0 172.28.128.1
ip route 172.16.0.0/16 172.16.222.1
```

### Router Adaptive Virtual Topology

#### Router Adaptive Virtual Topology Summary

Topology role: edge

| Hierarchy | Name | ID |
| --------- | ---- | -- |
| Region | Region2 | 2 |
| Zone | Region2-ZONE | 1 |
| Site | REGION2_SITE2 | 104 |

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
   site REGION2_SITE2 id 104
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
| 65000 | 10.2.2.2 |

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
| 10.2.2.65 | 65000 | default | - | all | - | - | - | - | True | - | - |
| 10.2.2.130 | 65022 | default | - | Inherited from peer group IPv4-UNDERLAY-PEERS | Inherited from peer group IPv4-UNDERLAY-PEERS | - | - | - | - | - | - |
| 10.255.0.1 | Inherited from peer group WAN-OVERLAY-PEERS | default | - | Inherited from peer group WAN-OVERLAY-PEERS | Inherited from peer group WAN-OVERLAY-PEERS | - | Inherited from peer group WAN-OVERLAY-PEERS(interval: 1000, min_rx: 1000, multiplier: 10) | - | - | - | Inherited from peer group WAN-OVERLAY-PEERS |
| 10.255.0.2 | Inherited from peer group WAN-OVERLAY-PEERS | default | - | Inherited from peer group WAN-OVERLAY-PEERS | Inherited from peer group WAN-OVERLAY-PEERS | - | Inherited from peer group WAN-OVERLAY-PEERS(interval: 1000, min_rx: 1000, multiplier: 10) | - | - | - | Inherited from peer group WAN-OVERLAY-PEERS |
| 10.2.2.130 | 65022 | customer1 | - | Inherited from peer group IPv4-UNDERLAY-PEERS | Inherited from peer group IPv4-UNDERLAY-PEERS | - | - | - | - | - | - |
| 10.2.2.130 | 65022 | customer2 | - | Inherited from peer group IPv4-UNDERLAY-PEERS | Inherited from peer group IPv4-UNDERLAY-PEERS | - | - | - | - | - | - |
| 10.2.2.130 | 65022 | customer3 | - | Inherited from peer group IPv4-UNDERLAY-PEERS | Inherited from peer group IPv4-UNDERLAY-PEERS | - | - | - | - | - | - |
| 10.2.2.130 | 65022 | customer4 | - | Inherited from peer group IPv4-UNDERLAY-PEERS | Inherited from peer group IPv4-UNDERLAY-PEERS | - | - | - | - | - | - |
| 10.2.2.130 | 65022 | customer5 | - | Inherited from peer group IPv4-UNDERLAY-PEERS | Inherited from peer group IPv4-UNDERLAY-PEERS | - | - | - | - | - | - |
| 10.2.2.130 | 65022 | customer6 | - | Inherited from peer group IPv4-UNDERLAY-PEERS | Inherited from peer group IPv4-UNDERLAY-PEERS | - | - | - | - | - | - |

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
| customer1 | 10.2.2.2:254 | connected |
| customer2 | 10.2.2.2:2 | connected |
| customer3 | 10.2.2.2:3 | connected |
| customer4 | 10.2.2.2:4 | connected |
| customer5 | 10.2.2.2:5 | connected |
| customer6 | 10.2.2.2:6 | connected |
| default | 10.2.2.2:1 | - |

#### Router BGP Device Configuration

```eos
!
router bgp 65000
   router-id 10.2.2.2
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
   neighbor 10.2.2.65 remote-as 65000
   neighbor 10.2.2.65 description r2-site2-wan1
   neighbor 10.2.2.65 route-reflector-client
   neighbor 10.2.2.65 update-source Dps1
   neighbor 10.2.2.65 route-map RM-WAN-HA-PEER-IN in
   neighbor 10.2.2.65 route-map RM-WAN-HA-PEER-OUT out
   neighbor 10.2.2.65 send-community
   neighbor 10.2.2.130 peer group IPv4-UNDERLAY-PEERS
   neighbor 10.2.2.130 remote-as 65022
   neighbor 10.2.2.130 description r2-site2-leaf1_Ethernet48
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
      neighbor 10.2.2.65 activate
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
      rd 10.2.2.2:254
      route-target import evpn 254:254
      route-target export evpn 254:254
      router-id 10.2.2.2
      neighbor 10.2.2.130 remote-as 65022
      neighbor 10.2.2.130 peer group IPv4-UNDERLAY-PEERS
      neighbor 10.2.2.130 description r2-site2-leaf1_Ethernet48.254_vrf_customer1
      redistribute connected
   !
   vrf customer2
      rd 10.2.2.2:2
      route-target import evpn 2:2
      route-target export evpn 2:2
      router-id 10.2.2.2
      neighbor 10.2.2.130 remote-as 65022
      neighbor 10.2.2.130 peer group IPv4-UNDERLAY-PEERS
      neighbor 10.2.2.130 description r2-site2-leaf1_Ethernet48.2_vrf_customer2
      redistribute connected
   !
   vrf customer3
      rd 10.2.2.2:3
      route-target import evpn 3:3
      route-target export evpn 3:3
      router-id 10.2.2.2
      neighbor 10.2.2.130 remote-as 65022
      neighbor 10.2.2.130 peer group IPv4-UNDERLAY-PEERS
      neighbor 10.2.2.130 description r2-site2-leaf1_Ethernet48.3_vrf_customer3
      redistribute connected
   !
   vrf customer4
      rd 10.2.2.2:4
      route-target import evpn 4:4
      route-target export evpn 4:4
      router-id 10.2.2.2
      neighbor 10.2.2.130 remote-as 65022
      neighbor 10.2.2.130 peer group IPv4-UNDERLAY-PEERS
      neighbor 10.2.2.130 description r2-site2-leaf1_Ethernet48.4_vrf_customer4
      redistribute connected
   !
   vrf customer5
      rd 10.2.2.2:5
      route-target import evpn 5:5
      route-target export evpn 5:5
      router-id 10.2.2.2
      neighbor 10.2.2.130 remote-as 65022
      neighbor 10.2.2.130 peer group IPv4-UNDERLAY-PEERS
      neighbor 10.2.2.130 description r2-site2-leaf1_Ethernet48.5_vrf_customer5
      redistribute connected
   !
   vrf customer6
      rd 10.2.2.2:6
      route-target import evpn 6:6
      route-target export evpn 6:6
      router-id 10.2.2.2
      neighbor 10.2.2.130 remote-as 65022
      neighbor 10.2.2.130 peer group IPv4-UNDERLAY-PEERS
      neighbor 10.2.2.130 description r2-site2-leaf1_Ethernet48.6_vrf_customer6
      redistribute connected
   !
   vrf default
      rd 10.2.2.2:1
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
| 10 | permit 10.2.2.0/26 eq 32 |

#### Prefix-lists Device Configuration

```eos
!
ip prefix-list PL-LOOPBACKS-EVPN-OVERLAY
   seq 10 permit 10.2.2.0/26 eq 32
```

### Route-maps

#### Route-maps Summary

##### RM-BGP-UNDERLAY-PEERS-IN

| Sequence | Type | Match | Set | Sub-Route-Map | Continue |
| -------- | ---- | ----- | --- | ------------- | -------- |
| 40 | permit | - | extcommunity soo 10.2.2.1:104 additive | - | - |

##### RM-BGP-UNDERLAY-PEERS-OUT

| Sequence | Type | Match | Set | Sub-Route-Map | Continue |
| -------- | ---- | ----- | --- | ------------- | -------- |
| 10 | permit | tag 50<br>route-type internal | metric 50 | - | - |
| 20 | permit | - | - | - | - |

##### RM-CONN-2-BGP

| Sequence | Type | Match | Set | Sub-Route-Map | Continue |
| -------- | ---- | ----- | --- | ------------- | -------- |
| 10 | permit | ip address prefix-list PL-LOOPBACKS-EVPN-OVERLAY | extcommunity soo 10.2.2.1:104 additive | - | - |

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
| 10 | permit | - | extcommunity soo 10.2.2.1:104 additive | - | - |

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
   set extcommunity soo 10.2.2.1:104 additive
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
   set extcommunity soo 10.2.2.1:104 additive
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
   set extcommunity soo 10.2.2.1:104 additive
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
| ECL-EVPN-SOO | permit | soo 10.2.2.1:104 |

#### IP Extended Community Lists Device Configuration

```eos
!
ip extcommunity-list ECL-EVPN-SOO permit soo 10.2.2.1:104
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
| Ethernet3 | - |  |

###### Static Peers

| Router IP | Name | IPv4 address(es) |
| --------- | ---- | ---------------- |
| 10.2.2.65 | r2-site2-wan1 | 10.10.10.0 |

##### Path Group rmpls2

| Setting | Value |
| ------  | ----- |
| Path Group ID | 102 |
| IPSec profile | CP-PROFILE |

###### Local Interfaces

| Interface name | Public address | STUN server profile(s) |
| -------------- | -------------- | ---------------------- |
| Ethernet1 | - | rmpls2-pf1-Ethernet1_6<br>rmpls2-pf2-Ethernet1_6 |

###### Dynamic Peers Settings

| Setting | Value |
| ------  | ----- |
| IP Local | - |
| IPSec | - |

###### Static Peers

| Router IP | Name | IPv4 address(es) |
| --------- | ---- | ---------------- |
| 10.255.0.1 | pf1 | 172.16.51.14 |
| 10.255.0.2 | pf2 | 172.16.52.14 |

#### Load-balance Policies

| Policy Name | Jitter (ms) | Latency (ms) | Loss Rate (%) | Path Groups (priority) | Lowest Hop Count |
| ----------- | ----------- | ------------ | ------------- | ---------------------- | ---------------- |
| LB-DEFAULT-AVT-POLICY-CONTROL-PLANE | - | - | - | LAN_HA (1)<br>rmpls2 (1) | False |
| LB-DEFAULT-AVT-POLICY-DEFAULT | - | - | - | LAN_HA (1)<br>rmpls2 (1) | False |
| LB-TELEPRES-AVT-POLICY-DEFAULT | - | - | - | LAN_HA (1)<br>rmpls2 (1) | False |
| LB-TELEPRES-AVT-POLICY-VOICE | 20 | 120 | 0.3 | LAN_HA (1)<br>rmpls2 (1) | True |

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
      local interface Ethernet3
      !
      peer static router-ip 10.2.2.65
         name r2-site2-wan1
         ipv4 address 10.10.10.0
   !
   path-group rmpls2 id 102
      ipsec profile CP-PROFILE
      !
      local interface Ethernet1
         stun server-profile rmpls2-pf1-Ethernet1_6 rmpls2-pf2-Ethernet1_6
      !
      peer dynamic
      !
      peer static router-ip 10.255.0.1
         name pf1
         ipv4 address 172.16.51.14
      !
      peer static router-ip 10.255.0.2
         name pf2
         ipv4 address 172.16.52.14
   !
   load-balance policy LB-DEFAULT-AVT-POLICY-CONTROL-PLANE
      path-group LAN_HA
      path-group rmpls2
   !
   load-balance policy LB-DEFAULT-AVT-POLICY-DEFAULT
      path-group LAN_HA
      path-group rmpls2
   !
   load-balance policy LB-TELEPRES-AVT-POLICY-DEFAULT
      path-group LAN_HA
      path-group rmpls2
   !
   load-balance policy LB-TELEPRES-AVT-POLICY-VOICE
      hop count lowest
      jitter 20
      latency 120
      loss-rate 0.3
      path-group LAN_HA
      path-group rmpls2
```

## STUN

### STUN Client

#### Server Profiles

| Server Profile | IP address | SSL Profile | Port |
| -------------- | ---------- | ----------- | ---- |
| rmpls2-pf1-Ethernet1_6 | 172.16.51.14 | - | 3478 |
| rmpls2-pf2-Ethernet1_6 | 172.16.52.14 | - | 3478 |

### STUN Device Configuration

```eos
!
stun
   client
      server-profile rmpls2-pf1-Ethernet1_6
         ip address 172.16.51.14
      server-profile rmpls2-pf2-Ethernet1_6
         ip address 172.16.52.14
```
