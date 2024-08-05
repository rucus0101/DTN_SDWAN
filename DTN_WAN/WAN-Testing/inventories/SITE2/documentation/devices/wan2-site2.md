# wan2-site2

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
- [Management Security](#management-security)
  - [Management Security Summary](#management-security-summary)
  - [Management Security SSL Profiles](#management-security-ssl-profiles)
  - [SSL profile STUN-DTLS Certificates Summary](#ssl-profile-stun-dtls-certificates-summary)
  - [Management Security Device Configuration](#management-security-device-configuration)
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
  - [AS Path Lists](#as-path-lists)
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
| Management1 | oob_management | oob | MGMT | 192.168.66.26/24 | 192.168.66.1 |

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
   ip address 192.168.66.26/24
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

## Management Security

### Management Security Summary

| Settings | Value |
| -------- | ----- |

### Management Security SSL Profiles

| SSL Profile Name | TLS protocol accepted | Certificate filename | Key filename | Cipher List | CRLs |
| ---------------- | --------------------- | -------------------- | ------------ | ----------- | ---- |
| STUN-DTLS | 1.2 | STUN-DTLS.crt | STUN-DTLS.key | - | - |

### SSL profile STUN-DTLS Certificates Summary

| Trust Certificates | Requirement | Policy | System |
| ------------------ | ----------- | ------ | ------ |
| aristaDeviceCertProvisionerDefaultRootCA.crt | - | - | - |

### Management Security Device Configuration

```eos
!
management security
   ssl profile STUN-DTLS
      tls versions 1.2
      trust certificate aristaDeviceCertProvisionerDefaultRootCA.crt
      certificate STUN-DTLS.crt key STUN-DTLS.key
```

## Monitoring

### TerminAttr Daemon

#### TerminAttr Daemon Summary

| CV Compression | CloudVision Servers | VRF | Authentication | Smash Excludes | Ingest Exclude | Bypass AAA |
| -------------- | ------------------- | --- | -------------- | -------------- | -------------- | ---------- |
| gzip | avd-cv-master-1.fun.aristanetworks.com:9910 | MGMT | token,/mnt/flash/token | ale,flexCounter,hardware,kni,pulse,strata | /Sysdb/cell/1/agent,/Sysdb/cell/2/agent | False |

#### TerminAttr Daemon Device Configuration

```eos
!
daemon TerminAttr
   exec /usr/bin/TerminAttr -cvaddr=avd-cv-master-1.fun.aristanetworks.com:9910 -cvauth=token,/mnt/flash/token -cvvrf=MGMT -smashexcludes=ale,flexCounter,hardware,kni,pulse,strata -ingestexclude=/Sysdb/cell/1/agent,/Sysdb/cell/2/agent -taillogs
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
| DP-IKE-POLICY | - | - | - | 10.102.255.2 |
| CP-IKE-POLICY | - | - | - | 10.102.255.2 |

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
      local-id 10.102.255.2
   !
   ike policy CP-IKE-POLICY
      local-id 10.102.255.2
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
| Dps1 | 10.102.255.2/32 | - | 9214 | Hardware: FLOW-TRACKER |  |

#### DPS Interfaces Device Configuration

```eos
!
interface Dps1
   description DPS Interface
   mtu 9214
   flow tracker hardware FLOW-TRACKER
   ip address 10.102.255.2/32
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
| Ethernet1.10 | P2P_LINK_TO_SPINE2-SITE2_Ethernet3.10_vrf_guest | l3dot1q | - | 10 |
| Ethernet1.20 | P2P_LINK_TO_SPINE2-SITE2_Ethernet3.20_vrf_data | l3dot1q | - | 20 |

##### IPv4

| Interface | Description | Type | Channel Group | IP Address | VRF |  MTU | Shutdown | ACL In | ACL Out |
| --------- | ----------- | -----| ------------- | ---------- | ----| ---- | -------- | ------ | ------- |
| Ethernet1 | P2P_LINK_TO_SPINE2-SITE2_Ethernet3 | routed | - | 10.102.0.131/31 | default | 9214 | True | - | - |
| Ethernet1.10 | P2P_LINK_TO_SPINE2-SITE2_Ethernet3.10_vrf_guest | l3dot1q | - | 10.102.0.131/31 | guest | 9214 | False | - | - |
| Ethernet1.20 | P2P_LINK_TO_SPINE2-SITE2_Ethernet3.20_vrf_data | l3dot1q | - | 10.102.0.131/31 | data | 9214 | False | - | - |
| Ethernet4 | isp-1_SITE2-INET-7 | routed | - | 100.64.7.2/24 | default | - | True | - | - |

#### Ethernet Interfaces Device Configuration

```eos
!
interface Ethernet1
   description P2P_LINK_TO_SPINE2-SITE2_Ethernet3
   shutdown
   mtu 9214
   no switchport
   ip address 10.102.0.131/31
!
interface Ethernet1.10
   description P2P_LINK_TO_SPINE2-SITE2_Ethernet3.10_vrf_guest
   no shutdown
   mtu 9214
   encapsulation dot1q vlan 10
   vrf guest
   ip address 10.102.0.131/31
!
interface Ethernet1.20
   description P2P_LINK_TO_SPINE2-SITE2_Ethernet3.20_vrf_data
   no shutdown
   mtu 9214
   encapsulation dot1q vlan 20
   vrf data
   ip address 10.102.0.131/31
!
interface Ethernet4
   description isp-1_SITE2-INET-7
   shutdown
   no switchport
   ip address 100.64.7.2/24
```

### Loopback Interfaces

#### Loopback Interfaces Summary

##### IPv4

| Interface | Description | VRF | IP Address |
| --------- | ----------- | --- | ---------- |
| Loopback0 | Router_ID | default | 10.102.254.2/32 |

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
   ip address 10.102.254.2/32
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
| data | 20 | - |
| default | 1 | - |
| guest | 10 | - |

#### VXLAN Interface Device Configuration

```eos
!
interface Vxlan1
   description wan2-site2_VTEP
   vxlan source-interface Dps1
   vxlan udp-port 4789
   vxlan vrf data vni 20
   vxlan vrf default vni 1
   vxlan vrf guest vni 10
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
| data | True |
| guest | True |
| MGMT | False |

#### IP Routing Device Configuration

```eos
!
ip routing
ip routing vrf data
ip routing vrf guest
no ip routing vrf MGMT
```

### IPv6 Routing

#### IPv6 Routing Summary

| VRF | Routing Enabled |
| --- | --------------- |
| default | False |
| data | false |
| guest | false |
| MGMT | false |

### Static Routes

#### Static Routes Summary

| VRF | Destination Prefix | Next Hop IP | Exit interface | Administrative Distance | Tag | Route Name | Metric |
| --- | ------------------ | ----------- | -------------- | ----------------------- | --- | ---------- | ------ |
| MGMT | 0.0.0.0/0 | 192.168.66.1 | - | 1 | - | - | - |
| default | 100.64.0.0/16 | 100.64.7.1 | - | 1 | - | - | - |

#### Static Routes Device Configuration

```eos
!
ip route vrf MGMT 0.0.0.0/0 192.168.66.1
ip route 100.64.0.0/16 100.64.7.1
```

### Router Adaptive Virtual Topology

#### Router Adaptive Virtual Topology Summary

Topology role: edge

| Hierarchy | Name | ID |
| --------- | ---- | -- |
| Region | Global | 1 |
| Zone | Global-ZONE | 1 |
| Site | SITE2 | 102 |

#### AVT Profiles

| Profile name | Load balance policy | Internet exit policy |
| ------------ | ------------------- | -------------------- |
| DATA-AVT-POLICY-DEFAULT | LB-DATA-AVT-POLICY-DEFAULT | - |
| DEFAULT-AVT-POLICY-CONTROL-PLANE | LB-DEFAULT-AVT-POLICY-CONTROL-PLANE | - |
| DEFAULT-AVT-POLICY-DEFAULT | LB-DEFAULT-AVT-POLICY-DEFAULT | - |
| GUEST-AVT-POLICY-DEFAULT | LB-GUEST-AVT-POLICY-DEFAULT | - |

#### AVT Policies

##### AVT policy DATA-AVT-POLICY

| Application profile | AVT Profile | Traffic Class | DSCP |
| ------------------- | ----------- | ------------- | ---- |
| default | DATA-AVT-POLICY-DEFAULT | - | - |

##### AVT policy DEFAULT-AVT-POLICY-WITH-CP

| Application profile | AVT Profile | Traffic Class | DSCP |
| ------------------- | ----------- | ------------- | ---- |
| APP-PROFILE-CONTROL-PLANE | DEFAULT-AVT-POLICY-CONTROL-PLANE | - | - |
| default | DEFAULT-AVT-POLICY-DEFAULT | - | - |

##### AVT policy GUEST-AVT-POLICY

| Application profile | AVT Profile | Traffic Class | DSCP |
| ------------------- | ----------- | ------------- | ---- |
| default | GUEST-AVT-POLICY-DEFAULT | - | - |

#### VRFs configuration

##### VRF data

| AVT policy |
| ---------- |
| DATA-AVT-POLICY |

| AVT Profile | AVT ID |
| ----------- | ------ |
| DATA-AVT-POLICY-DEFAULT | 1 |

##### VRF default

| AVT policy |
| ---------- |
| DEFAULT-AVT-POLICY-WITH-CP |

| AVT Profile | AVT ID |
| ----------- | ------ |
| DEFAULT-AVT-POLICY-DEFAULT | 1 |
| DEFAULT-AVT-POLICY-CONTROL-PLANE | 254 |

##### VRF guest

| AVT policy |
| ---------- |
| GUEST-AVT-POLICY |

| AVT Profile | AVT ID |
| ----------- | ------ |
| GUEST-AVT-POLICY-DEFAULT | 1 |

#### Router Adaptive Virtual Topology Configuration

```eos
!
router adaptive-virtual-topology
   topology role edge
   region Global id 1
   zone Global-ZONE id 1
   site SITE2 id 102
   !
   policy DATA-AVT-POLICY
      !
      match application-profile default
         avt profile DATA-AVT-POLICY-DEFAULT
   !
   policy DEFAULT-AVT-POLICY-WITH-CP
      !
      match application-profile APP-PROFILE-CONTROL-PLANE
         avt profile DEFAULT-AVT-POLICY-CONTROL-PLANE
      !
      match application-profile default
         avt profile DEFAULT-AVT-POLICY-DEFAULT
   !
   policy GUEST-AVT-POLICY
      !
      match application-profile default
         avt profile GUEST-AVT-POLICY-DEFAULT
   !
   profile DATA-AVT-POLICY-DEFAULT
      path-selection load-balance LB-DATA-AVT-POLICY-DEFAULT
   !
   profile DEFAULT-AVT-POLICY-CONTROL-PLANE
      path-selection load-balance LB-DEFAULT-AVT-POLICY-CONTROL-PLANE
   !
   profile DEFAULT-AVT-POLICY-DEFAULT
      path-selection load-balance LB-DEFAULT-AVT-POLICY-DEFAULT
   !
   profile GUEST-AVT-POLICY-DEFAULT
      path-selection load-balance LB-GUEST-AVT-POLICY-DEFAULT
   !
   vrf data
      avt policy DATA-AVT-POLICY
      avt profile DATA-AVT-POLICY-DEFAULT id 1
   !
   vrf default
      avt policy DEFAULT-AVT-POLICY-WITH-CP
      avt profile DEFAULT-AVT-POLICY-DEFAULT id 1
      avt profile DEFAULT-AVT-POLICY-CONTROL-PLANE id 254
   !
   vrf guest
      avt policy GUEST-AVT-POLICY
      avt profile GUEST-AVT-POLICY-DEFAULT id 1
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
| 65000 | 10.102.254.2 |

| BGP Tuning |
| ---------- |
| no bgp default ipv4-unicast |
| maximum-paths 16 |

#### Router BGP Peer Groups

##### IPv4-UNDERLAY-PEERS

| Settings | Value |
| -------- | ----- |
| Address Family | ipv4 |
| Allowas-in | Allowed, allowed 1 times |
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
| 10.102.0.130 | - | default | - | Inherited from peer group IPv4-UNDERLAY-PEERS | Inherited from peer group IPv4-UNDERLAY-PEERS | Inherited from peer group IPv4-UNDERLAY-PEERS | - | - | - | - | - |
| 10.255.0.1 | Inherited from peer group WAN-OVERLAY-PEERS | default | - | Inherited from peer group WAN-OVERLAY-PEERS | Inherited from peer group WAN-OVERLAY-PEERS | - | Inherited from peer group WAN-OVERLAY-PEERS(interval: 1000, min_rx: 1000, multiplier: 10) | - | - | - | Inherited from peer group WAN-OVERLAY-PEERS |
| 10.255.0.2 | Inherited from peer group WAN-OVERLAY-PEERS | default | - | Inherited from peer group WAN-OVERLAY-PEERS | Inherited from peer group WAN-OVERLAY-PEERS | - | Inherited from peer group WAN-OVERLAY-PEERS(interval: 1000, min_rx: 1000, multiplier: 10) | - | - | - | Inherited from peer group WAN-OVERLAY-PEERS |
| 10.102.0.130 | - | data | - | Inherited from peer group IPv4-UNDERLAY-PEERS | Inherited from peer group IPv4-UNDERLAY-PEERS | Inherited from peer group IPv4-UNDERLAY-PEERS | - | - | - | - | - |
| 10.102.0.130 | - | guest | - | Inherited from peer group IPv4-UNDERLAY-PEERS | Inherited from peer group IPv4-UNDERLAY-PEERS | Inherited from peer group IPv4-UNDERLAY-PEERS | - | - | - | - | - |

#### Router BGP EVPN Address Family

##### EVPN Peer Groups

| Peer Group | Activate | Encapsulation |
| ---------- | -------- | ------------- |
| WAN-OVERLAY-PEERS | True | default |

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
| data | 10.102.254.2:20 | connected |
| default | 10.102.254.2:1 | - |
| guest | 10.102.254.2:10 | connected |

#### Router BGP Device Configuration

```eos
!
router bgp 65000
   router-id 10.102.254.2
   maximum-paths 16
   no bgp default ipv4-unicast
   neighbor IPv4-UNDERLAY-PEERS peer group
   neighbor IPv4-UNDERLAY-PEERS allowas-in 1
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
   neighbor 10.102.0.130 peer group IPv4-UNDERLAY-PEERS
   neighbor 10.102.0.130 description spine2-site2_Ethernet3
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
   vrf data
      rd 10.102.254.2:20
      route-target import evpn 20:20
      route-target export evpn 20:20
      router-id 10.102.254.2
      neighbor 10.102.0.130 peer group IPv4-UNDERLAY-PEERS
      neighbor 10.102.0.130 description spine2-site2_Ethernet3.20_vrf_data
      redistribute connected
   !
   vrf default
      rd 10.102.254.2:1
      route-target import evpn 1:1
      route-target export evpn 1:1
      route-target export evpn route-map RM-EVPN-EXPORT-VRF-DEFAULT
   !
   vrf guest
      rd 10.102.254.2:10
      route-target import evpn 10:10
      route-target export evpn 10:10
      router-id 10.102.254.2
      neighbor 10.102.0.130 peer group IPv4-UNDERLAY-PEERS
      neighbor 10.102.0.130 description spine2-site2_Ethernet3.10_vrf_guest
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

## Filters

### Prefix-lists

#### Prefix-lists Summary

##### PL-LOOPBACKS-EVPN-OVERLAY

| Sequence | Action |
| -------- | ------ |
| 10 | permit 10.102.254.0/25 eq 32 |

##### PL-WAN-HA-PEER-PREFIXES

| Sequence | Action |
| -------- | ------ |
| 10 | permit 10.102.0.128/31 |

##### PL-WAN-HA-PREFIXES

| Sequence | Action |
| -------- | ------ |
| 10 | permit 10.102.0.130/31 |

#### Prefix-lists Device Configuration

```eos
!
ip prefix-list PL-LOOPBACKS-EVPN-OVERLAY
   seq 10 permit 10.102.254.0/25 eq 32
!
ip prefix-list PL-WAN-HA-PEER-PREFIXES
   seq 10 permit 10.102.0.128/31
!
ip prefix-list PL-WAN-HA-PREFIXES
   seq 10 permit 10.102.0.130/31
```

### Route-maps

#### Route-maps Summary

##### RM-BGP-UNDERLAY-PEERS-IN

| Sequence | Type | Match | Set | Sub-Route-Map | Continue |
| -------- | ---- | ----- | --- | ------------- | -------- |
| 10 | permit | ip address prefix-list PL-WAN-HA-PEER-PREFIXES | - | - | - |
| 20 | permit | extcommunity ECL-EVPN-SOO | as-path match all replacement auto auto | - | - |
| 30 | permit | as-path ASPATH-WAN | community no-advertise | - | - |
| 40 | permit | - | extcommunity soo 10.102.254.1:102 additive | - | - |

##### RM-BGP-UNDERLAY-PEERS-OUT

| Sequence | Type | Match | Set | Sub-Route-Map | Continue |
| -------- | ---- | ----- | --- | ------------- | -------- |
| 10 | permit | extcommunity ECL-EVPN-SOO | - | - | - |
| 20 | permit | route-type internal | - | - | - |
| 30 | permit | ip address prefix-list PL-WAN-HA-PREFIXES | - | - | - |

##### RM-CONN-2-BGP

| Sequence | Type | Match | Set | Sub-Route-Map | Continue |
| -------- | ---- | ----- | --- | ------------- | -------- |
| 10 | permit | ip address prefix-list PL-LOOPBACKS-EVPN-OVERLAY | extcommunity soo 10.102.254.1:102 additive | - | - |
| 50 | permit | ip address prefix-list PL-WAN-HA-PREFIXES | - | - | - |

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
| 10 | permit | - | extcommunity soo 10.102.254.1:102 additive | - | - |

#### Route-maps Device Configuration

```eos
!
route-map RM-BGP-UNDERLAY-PEERS-IN permit 10
   description Allow WAN HA peer interface prefixes
   match ip address prefix-list PL-WAN-HA-PEER-PREFIXES
!
route-map RM-BGP-UNDERLAY-PEERS-IN permit 20
   description Allow prefixes originated from the HA peer
   match extcommunity ECL-EVPN-SOO
   set as-path match all replacement auto auto
!
route-map RM-BGP-UNDERLAY-PEERS-IN permit 30
   description Use WAN routes from HA peer as backup
   match as-path ASPATH-WAN
   set community no-advertise
!
route-map RM-BGP-UNDERLAY-PEERS-IN permit 40
   description Mark prefixes originated from the LAN
   set extcommunity soo 10.102.254.1:102 additive
!
route-map RM-BGP-UNDERLAY-PEERS-OUT permit 10
   description Advertise local routes towards LAN
   match extcommunity ECL-EVPN-SOO
!
route-map RM-BGP-UNDERLAY-PEERS-OUT permit 20
   description Advertise routes received from WAN iBGP towards LAN
   match route-type internal
!
route-map RM-BGP-UNDERLAY-PEERS-OUT permit 30
   description Advertise WAN HA prefixes towards LAN
   match ip address prefix-list PL-WAN-HA-PREFIXES
!
route-map RM-CONN-2-BGP permit 10
   match ip address prefix-list PL-LOOPBACKS-EVPN-OVERLAY
   set extcommunity soo 10.102.254.1:102 additive
!
route-map RM-CONN-2-BGP permit 50
   match ip address prefix-list PL-WAN-HA-PREFIXES
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
   set extcommunity soo 10.102.254.1:102 additive
```

### IP Extended Community Lists

#### IP Extended Community Lists Summary

| List Name | Type | Extended Communities |
| --------- | ---- | -------------------- |
| ECL-EVPN-SOO | permit | soo 10.102.254.1:102 |

#### IP Extended Community Lists Device Configuration

```eos
!
ip extcommunity-list ECL-EVPN-SOO permit soo 10.102.254.1:102
```

### AS Path Lists

#### AS Path Lists Summary

| List Name | Type | Match | Origin |
| --------- | ---- | ----- | ------ |
| ASPATH-WAN | permit | 65000 | any |

#### AS Path Lists Device Configuration

```eos
!
ip as-path access-list ASPATH-WAN permit 65000 any
```

## VRF Instances

### VRF Instances Summary

| VRF Name | IP Routing |
| -------- | ---------- |
| data | enabled |
| guest | enabled |
| MGMT | disabled |

### VRF Instances Device Configuration

```eos
!
vrf instance data
!
vrf instance guest
!
vrf instance MGMT
```

## Application Traffic Recognition

### Applications

#### IPv4 Applications

| Name | Source Prefix | Destination Prefix | Protocols | Protocol Ranges | TCP Source Port Set | TCP Destination Port Set | UDP Source Port Set | UDP Destination Port Set |
| ---- | ------------- | ------------------ | --------- | --------------- | ------------------- | ------------------------ | ------------------- | ------------------------ |
| APP-CONTROL-PLANE | - | PFX-PATHFINDERS | - | - | - | - | - | - |

### Application Profiles

#### Application Profile Name APP-PROFILE-CONTROL-PLANE

| Type | Name | Service |
| ---- | ---- | ------- |
| application | APP-CONTROL-PLANE | - |

### Field Sets

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
   application-profile APP-PROFILE-CONTROL-PLANE
      application APP-CONTROL-PLANE
   !
   field-set ipv4 prefix PFX-PATHFINDERS
      10.255.0.1/32 10.255.0.2/32
```

### Router Path-selection

#### TCP MSS Ceiling Configuration

| IPV4 segment size | Direction |
| ----------------- | --------- |
| auto | ingress |

#### Path Groups

##### Path Group internet

| Setting | Value |
| ------  | ----- |
| Path Group ID | 102 |
| IPSec profile | CP-PROFILE |

###### Local Interfaces

| Interface name | Public address | STUN server profile(s) |
| -------------- | -------------- | ---------------------- |
| Ethernet4 | - | internet-pf1-Ethernet2<br>internet-pf2-Ethernet2 |

###### Dynamic Peers Settings

| Setting | Value |
| ------  | ----- |
| IP Local | - |
| IPSec | - |

###### Static Peers

| Router IP | Name | IPv4 address(es) |
| --------- | ---- | ---------------- |
| 10.255.0.1 | pf1 | 100.64.1.2 |
| 10.255.0.2 | pf2 | 100.64.2.2 |

##### Path Group LAN_HA

| Setting | Value |
| ------  | ----- |
| Path Group ID | 65535 |
| IPSec profile | DP-PROFILE |
| Flow assignment | LAN |

###### Local Interfaces

| Interface name | Public address | STUN server profile(s) |
| -------------- | -------------- | ---------------------- |
| Ethernet1 | - |  |

###### Static Peers

| Router IP | Name | IPv4 address(es) |
| --------- | ---- | ---------------- |
| 10.102.255.1 | wan1-site2 | 10.102.0.129 |

#### Load-balance Policies

| Policy Name | Jitter (ms) | Latency (ms) | Loss Rate (%) | Path Groups (priority) | Lowest Hop Count |
| ----------- | ----------- | ------------ | ------------- | ---------------------- | ---------------- |
| LB-DATA-AVT-POLICY-DEFAULT | - | - | - | LAN_HA (1)<br>internet (2) | False |
| LB-DEFAULT-AVT-POLICY-CONTROL-PLANE | - | - | - | internet (1)<br>LAN_HA (1) | False |
| LB-DEFAULT-AVT-POLICY-DEFAULT | - | - | - | internet (1)<br>LAN_HA (1) | False |
| LB-GUEST-AVT-POLICY-DEFAULT | - | - | - | internet (1)<br>LAN_HA (1) | False |

#### Router Path-selection Device Configuration

```eos
!
router path-selection
   tcp mss ceiling ipv4 ingress
   !
   path-group internet id 102
      ipsec profile CP-PROFILE
      !
      local interface Ethernet4
         stun server-profile internet-pf1-Ethernet2 internet-pf2-Ethernet2
      !
      peer dynamic
      !
      peer static router-ip 10.255.0.1
         name pf1
         ipv4 address 100.64.1.2
      !
      peer static router-ip 10.255.0.2
         name pf2
         ipv4 address 100.64.2.2
   !
   path-group LAN_HA id 65535
      ipsec profile DP-PROFILE
      flow assignment lan
      !
      local interface Ethernet1
      !
      peer static router-ip 10.102.255.1
         name wan1-site2
         ipv4 address 10.102.0.129
   !
   load-balance policy LB-DATA-AVT-POLICY-DEFAULT
      path-group LAN_HA
      path-group internet priority 2
   !
   load-balance policy LB-DEFAULT-AVT-POLICY-CONTROL-PLANE
      path-group internet
      path-group LAN_HA
   !
   load-balance policy LB-DEFAULT-AVT-POLICY-DEFAULT
      path-group internet
      path-group LAN_HA
   !
   load-balance policy LB-GUEST-AVT-POLICY-DEFAULT
      path-group internet
      path-group LAN_HA
```

## STUN

### STUN Client

#### Server Profiles

| Server Profile | IP address | SSL Profile | Port |
| -------------- | ---------- | ----------- | ---- |
| internet-pf1-Ethernet2 | 100.64.1.2/24 | STUN-DTLS | 3478 |
| internet-pf2-Ethernet2 | 100.64.2.2/24 | STUN-DTLS | 3478 |

### STUN Device Configuration

```eos
!
stun
   client
      server-profile internet-pf1-Ethernet2
         ip address 100.64.1.2/24
         ssl profile STUN-DTLS
      server-profile internet-pf2-Ethernet2
         ip address 100.64.2.2/24
         ssl profile STUN-DTLS
```
