# Site-DRF-INET-SDWAN

## Table of Contents

- [Management](#management)
  - [Agents](#agents)
  - [Management Interfaces](#management-interfaces)
  - [IP Name Servers](#ip-name-servers)
  - [Domain Lookup](#domain-lookup)
  - [NTP](#ntp)
  - [Management SSH](#management-ssh)
  - [Management API HTTP](#management-api-http)
- [Authentication](#authentication)
  - [Local Users](#local-users)
- [Monitoring](#monitoring)
  - [TerminAttr Daemon](#terminattr-daemon)
  - [Flow Tracking](#flow-tracking)
- [Spanning Tree](#spanning-tree)
  - [Spanning Tree Summary](#spanning-tree-summary)
  - [Spanning Tree Device Configuration](#spanning-tree-device-configuration)
- [Internal VLAN Allocation Policy](#internal-vlan-allocation-policy)
  - [Internal VLAN Allocation Policy Summary](#internal-vlan-allocation-policy-summary)
  - [Internal VLAN Allocation Policy Device Configuration](#internal-vlan-allocation-policy-device-configuration)
- [IP Security](#ip-security)
  - [IKE policies](#ike-policies)
  - [Security Association policies](#security-association-policies)
  - [IPSec profiles](#ipsec-profiles)
  - [Key controller](#key-controller)
  - [IP Security Device Configuration](#ip-security-device-configuration)
- [Interfaces](#interfaces)
  - [Switchport Default](#switchport-default)
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
- [VRF Instances](#vrf-instances)
  - [VRF Instances Summary](#vrf-instances-summary)
  - [VRF Instances Device Configuration](#vrf-instances-device-configuration)
- [System L1](#system-l1)
  - [Unsupported Interface Configurations](#unsupported-interface-configurations)
  - [System L1 Device Configuration](#system-l1-device-configuration)
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
| KERNELFIB_PROGRAM_ALL_ECMP | 'true' |

#### Agents Device Configuration

```eos
!
agent KernelFib environment KERNELFIB_PROGRAM_ALL_ECMP='true'
```

### Management Interfaces

#### Management Interfaces Summary

##### IPv4

| Management Interface | Description | Type | VRF | IP Address | Gateway |
| -------------------- | ----------- | ---- | --- | ---------- | ------- |
| Management1 | oob_management | oob | default | 10.90.245.94/24 | 10.90.245.1 |

##### IPv6

| Management Interface | Description | Type | VRF | IPv6 Address | IPv6 Gateway |
| -------------------- | ----------- | ---- | --- | ------------ | ------------ |
| Management1 | oob_management | oob | default | - | - |

#### Management Interfaces Device Configuration

```eos
!
interface Management1
   description oob_management
   no shutdown
   ip address 10.90.245.94/24
```

### IP Name Servers

#### IP Name Servers Summary

| Name Server | VRF | Priority |
| ----------- | --- | -------- |
| 10.14.0.1 | default | - |
| 172.22.22.40 | default | - |
| 8.8.4.4 | default | 4 |
| 8.8.8.8 | default | 4 |

#### IP Name Servers Device Configuration

```eos
ip name-server vrf default 8.8.4.4 priority 4
ip name-server vrf default 8.8.8.8 priority 4
ip name-server vrf default 10.14.0.1
ip name-server vrf default 172.22.22.40
```

### Domain Lookup

#### DNS Domain Lookup Summary

| Source interface | vrf |
| ---------------- | --- |
| Management1 | - |

#### DNS Domain Lookup Device Configuration

```eos
ip domain lookup source-interface Management1
```

### NTP

#### NTP Summary

##### NTP Local Interface

| Interface | VRF |
| --------- | --- |
| Management1 | default |

##### NTP Servers

| Server | VRF | Preferred | Burst | iBurst | Version | Min Poll | Max Poll | Local-interface | Key |
| ------ | --- | --------- | ----- | ------ | ------- | -------- | -------- | --------------- | --- |
| 10.41.194.6 | default | True | - | - | - | - | - | - | - |
| 10.85.14.245 | default | - | - | - | - | - | - | - | - |
| time.google.com | default | - | - | - | - | - | - | - | - |

#### NTP Device Configuration

```eos
!
ntp local-interface Management1
ntp server 10.41.194.6 prefer
ntp server 10.85.14.245
ntp server time.google.com
```

### Management SSH

#### SSH Timeout and Management

| Idle Timeout | SSH Management |
| ------------ | -------------- |
| default | Enabled |

#### Max number of SSH sessions limit and per-host limit

| Connection Limit | Max from a single Host |
| ---------------- | ---------------------- |
| - | - |

#### Ciphers and Algorithms

| Ciphers | Key-exchange methods | MAC algorithms | Hostkey server algorithms |
|---------|----------------------|----------------|---------------------------|
| default | default | default | default |


#### Management SSH Device Configuration

```eos
!
management ssh
   client-alive interval 180
```

### Management API HTTP

#### Management API HTTP Summary

| HTTP | HTTPS | Default Services |
| ---- | ----- | ---------------- |
| False | True | - |

#### Management API VRF Access

| VRF Name | IPv4 ACL | IPv6 ACL |
| -------- | -------- | -------- |
| default | - | - |

#### Management API HTTP Device Configuration

```eos
!
management api http-commands
   protocol https
   no shutdown
   !
   vrf default
      no shutdown
```

## Authentication

### Local Users

#### Local Users Summary

| User | Privilege | Role | Disabled | Shell |
| ---- | --------- | ---- | -------- | ----- |
| admin | 15 | network-admin | False | - |

#### Local Users Device Configuration

```eos
!
username admin privilege 15 role network-admin secret sha512 <removed>
```

## Monitoring

### TerminAttr Daemon

#### TerminAttr Daemon Summary

| CV Compression | CloudVision Servers | VRF | Authentication | Smash Excludes | Ingest Exclude | Bypass AAA |
| -------------- | ------------------- | --- | -------------- | -------------- | -------------- | ---------- |
| gzip | apiserver.cv-staging.corp.arista.io:443 | default | token-secure,/tmp/cv-onboarding-token | ale,flexCounter,hardware,kni,pulse,strata | /Sysdb/cell/1/agent,/Sysdb/cell/2/agent | True |

#### TerminAttr Daemon Device Configuration

```eos
!
daemon TerminAttr
   exec /usr/bin/TerminAttr -cvaddr=apiserver.cv-staging.corp.arista.io:443 -cvauth=token-secure,/tmp/cv-onboarding-token -cvvrf=default -disableaaa -smashexcludes=ale,flexCounter,hardware,kni,pulse,strata -ingestexclude=/Sysdb/cell/1/agent,/Sysdb/cell/2/agent -taillogs
   no shutdown
```

### Flow Tracking

#### Flow Tracking Hardware

##### Trackers Summary

| Tracker Name | Record Export On Inactive Timeout | Record Export On Interval | Number of Exporters | Applied On |
| ------------ | --------------------------------- | ------------------------- | ------------------- | ---------- |
| WAN-FLOW-TRACKER | 70000 | 5000 | 1 | Ethernet1<br>Ethernet2 |

##### Exporters Summary

| Tracker Name | Exporter Name | Collector IP/Host | Collector Port | Local Interface |
| ------------ | ------------- | ----------------- | -------------- | --------------- |
| WAN-FLOW-TRACKER | DPI-EXPORTER | - | - | Loopback0 |

#### Flow Tracking Device Configuration

```eos
!
flow tracking hardware
   tracker WAN-FLOW-TRACKER
      record export on inactive timeout 70000
      record export on interval 5000
      exporter DPI-EXPORTER
         collector 127.0.0.1
         local interface Loopback0
         template interval 5000
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

## IP Security

### IKE policies

| Policy name | IKE lifetime | Encryption | DH group | Local ID |
| ----------- | ------------ | ---------- | -------- | -------- |
| DP-IKE-POLICY | - | - | - | 192.168.110.1 |
| CP-IKE-POLICY | - | - | - | 192.168.110.1 |

### Security Association policies

| Policy name | ESP Integrity | ESP Encryption | PFS DH Group |
| ----------- | ------------- | -------------- | ------------ |
| DP-SA-POLICY | - | aes128 | 14 |
| CP-SA-POLICY | - | aes128 | 14 |

### IPSec profiles

| Profile name | IKE policy | SA policy | Connection | DPD Interval | DPD Time | DPD action | Mode |
| ------------ | ---------- | ----------| ---------- | ------------ | -------- | ---------- | ---- |
| DP-PROFILE | DP-IKE-POLICY | DP-SA-POLICY | start | - | - | - | transport |
| CP-PROFILE | CP-IKE-POLICY | CP-SA-POLICY | start | - | - | - | transport |

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
      local-id 192.168.110.1
   !
   ike policy CP-IKE-POLICY
      local-id 192.168.110.1
   !
   sa policy DP-SA-POLICY
      esp encryption aes128
      pfs dh-group 14
   !
   sa policy CP-SA-POLICY
      esp encryption aes128
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

### Switchport Default

#### Switchport Defaults Summary

- Default Switchport Mode: routed

#### Switchport Default Device Configuration

```eos
!
switchport default mode routed
```

### DPS Interfaces

#### DPS Interfaces Summary

| Interface | IP address | Shutdown | MTU | Flow tracker(s) | TCP MSS Ceiling |
| --------- | ---------- | -------- | --- | --------------- | --------------- |
| Dps1 | 192.168.110.1/32 | - | - | Hardware: WAN-FLOW-TRACKER |  |

#### DPS Interfaces Device Configuration

```eos
!
interface Dps1
   description DPS Interface
   flow tracker hardware WAN-FLOW-TRACKER
   ip address 192.168.110.1/32
```

### Ethernet Interfaces

#### Ethernet Interfaces Summary

##### L2

| Interface | Description | Mode | VLANs | Native VLAN | Trunk Group | Channel-Group |
| --------- | ----------- | ---- | ----- | ----------- | ----------- | ------------- |

*Inherited from Port-Channel Interface

##### IPv4

| Interface | Description | Type | Channel Group | IP Address | VRF |  MTU | Shutdown | ACL In | ACL Out |
| --------- | ----------- | -----| ------------- | ---------- | ----| ---- | -------- | ------ | ------- |
| Ethernet1 | - | routed | - | 10.90.244.34/24 | default | - | False | - | - |
| Ethernet2 | - | routed | - | 172.29.2.3/24 | default | - | False | - | - |

#### Ethernet Interfaces Device Configuration

```eos
!
interface Ethernet1
   no shutdown
   no switchport
   flow tracker hardware WAN-FLOW-TRACKER
   ip address 10.90.244.34/24
!
interface Ethernet2
   no shutdown
   no switchport
   flow tracker hardware WAN-FLOW-TRACKER
   ip address 172.29.2.3/24
```

### Loopback Interfaces

#### Loopback Interfaces Summary

##### IPv4

| Interface | Description | VRF | IP Address |
| --------- | ----------- | --- | ---------- |
| Loopback0 | Router_ID | default | 10.254.110.1/32 |

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
   ip address 10.254.110.1/32
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
| default | 101 | - |
| prod | 102 | - |

#### VXLAN Interface Device Configuration

```eos
!
interface Vxlan1
   description Site-DRF-INET-SDWAN_VTEP
   vxlan source-interface Dps1
   vxlan udp-port 4789
   vxlan vrf default vni 101
   vxlan vrf prod vni 102
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
| prod | True |

#### IP Routing Device Configuration

```eos
!
ip routing
ip routing vrf prod
```

### IPv6 Routing

#### IPv6 Routing Summary

| VRF | Routing Enabled |
| --- | --------------- |
| default | False |
| default | false |
| prod | false |

### Static Routes

#### Static Routes Summary

| VRF | Destination Prefix | Next Hop IP | Exit interface | Administrative Distance | Tag | Route Name | Metric |
| --- | ------------------ | ----------- | -------------- | ----------------------- | --- | ---------- | ------ |
| default | 0.0.0.0/0 | 10.90.245.1 | - | 1 | - | - | - |

#### Static Routes Device Configuration

```eos
!
ip route 0.0.0.0/0 10.90.245.1
```

### Router Adaptive Virtual Topology

#### Router Adaptive Virtual Topology Summary

Topology role: edge

| Hierarchy | Name | ID |
| --------- | ---- | -- |
| Region | Global | 1 |
| Zone | DEFAULT-ZONE | 1 |
| Site | Site-DRF | 110 |

#### AVT Profiles

| Profile name | Load balance policy | Internet exit policy |
| ------------ | ------------------- | -------------------- |
| CONTROL-PLANE-PROFILE | LB-CONTROL-PLANE-PROFILE | - |
| DEFAULT-AVT-POLICY-DEFAULT | LB-DEFAULT-AVT-POLICY-DEFAULT | - |
| DEFAULT-PROD-POLICY-DEFAULT | LB-DEFAULT-PROD-POLICY-DEFAULT | - |

#### AVT Policies

##### AVT policy DEFAULT-AVT-POLICY-WITH-CP

| Application profile | AVT Profile | Traffic Class | DSCP |
| ------------------- | ----------- | ------------- | ---- |
| CONTROL-PLANE-APPLICATION-PROFILE | CONTROL-PLANE-PROFILE | - | - |
| default | DEFAULT-AVT-POLICY-DEFAULT | - | - |

##### AVT policy DEFAULT-PROD-POLICY

| Application profile | AVT Profile | Traffic Class | DSCP |
| ------------------- | ----------- | ------------- | ---- |
| default | DEFAULT-PROD-POLICY-DEFAULT | - | - |

#### VRFs configuration

##### VRF default

| AVT policy |
| ---------- |
| DEFAULT-AVT-POLICY-WITH-CP |

| AVT Profile | AVT ID |
| ----------- | ------ |
| DEFAULT-AVT-POLICY-DEFAULT | 1 |
| CONTROL-PLANE-PROFILE | 254 |

##### VRF prod

| AVT policy |
| ---------- |
| DEFAULT-PROD-POLICY |

| AVT Profile | AVT ID |
| ----------- | ------ |
| DEFAULT-PROD-POLICY-DEFAULT | 1 |

#### Router Adaptive Virtual Topology Configuration

```eos
!
router adaptive-virtual-topology
   topology role edge
   region Global id 1
   zone DEFAULT-ZONE id 1
   site Site-DRF id 110
   !
   policy DEFAULT-AVT-POLICY-WITH-CP
      !
      match application-profile CONTROL-PLANE-APPLICATION-PROFILE
         avt profile CONTROL-PLANE-PROFILE
      !
      match application-profile default
         avt profile DEFAULT-AVT-POLICY-DEFAULT
   !
   policy DEFAULT-PROD-POLICY
      !
      match application-profile default
         avt profile DEFAULT-PROD-POLICY-DEFAULT
   !
   profile CONTROL-PLANE-PROFILE
      path-selection load-balance LB-CONTROL-PLANE-PROFILE
   !
   profile DEFAULT-AVT-POLICY-DEFAULT
      path-selection load-balance LB-DEFAULT-AVT-POLICY-DEFAULT
   !
   profile DEFAULT-PROD-POLICY-DEFAULT
      path-selection load-balance LB-DEFAULT-PROD-POLICY-DEFAULT
   !
   vrf default
      avt policy DEFAULT-AVT-POLICY-WITH-CP
      avt profile DEFAULT-AVT-POLICY-DEFAULT id 1
      avt profile CONTROL-PLANE-PROFILE id 254
   !
   vrf prod
      avt policy DEFAULT-PROD-POLICY
      avt profile DEFAULT-PROD-POLICY-DEFAULT id 1
```

### Router Traffic-Engineering

- Traffic Engineering is enabled.

#### Router Traffic Engineering Device Configuration

```eos
!
router traffic-engineering
```

### Router BGP

#### Router BGP Summary

| BGP AS | Router ID |
| ------ | --------- |
| 65199 | 10.254.110.1 |

| BGP Tuning |
| ---------- |
| no bgp default ipv4-unicast |
| maximum-paths 16 |

#### Router BGP Peer Groups

##### WAN-OVERLAY-PEERS

| Settings | Value |
| -------- | ----- |
| Address Family | wan |
| Remote AS | 65199 |
| Source | Dps1 |
| BFD | True |
| Send community | all |
| Maximum routes | 0 (no limit) |

#### BGP Neighbors

| Neighbor | Remote AS | VRF | Shutdown | Send-community | Maximum-routes | Allowas-in | BFD | RIB Pre-Policy Retain | Route-Reflector Client | Passive | TTL Max Hops |
| -------- | --------- | --- | -------- | -------------- | -------------- | ---------- | --- | --------------------- | ---------------------- | ------- | ------------ |
| 192.168.99.1 | Inherited from peer group WAN-OVERLAY-PEERS | default | - | Inherited from peer group WAN-OVERLAY-PEERS | Inherited from peer group WAN-OVERLAY-PEERS | - | Inherited from peer group WAN-OVERLAY-PEERS | - | - | - | - |
| 192.168.99.2 | Inherited from peer group WAN-OVERLAY-PEERS | default | - | Inherited from peer group WAN-OVERLAY-PEERS | Inherited from peer group WAN-OVERLAY-PEERS | - | Inherited from peer group WAN-OVERLAY-PEERS | - | - | - | - |

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
| default | 10.254.110.1:101 | - |
| prod | 10.254.110.1:102 | connected |

#### Router BGP Device Configuration

```eos
!
router bgp 65199
   router-id 10.254.110.1
   maximum-paths 16
   no bgp default ipv4-unicast
   neighbor WAN-OVERLAY-PEERS peer group
   neighbor WAN-OVERLAY-PEERS remote-as 65199
   neighbor WAN-OVERLAY-PEERS update-source Dps1
   neighbor WAN-OVERLAY-PEERS bfd
   neighbor WAN-OVERLAY-PEERS password 7 <removed>
   neighbor WAN-OVERLAY-PEERS send-community
   neighbor WAN-OVERLAY-PEERS maximum-routes 0
   neighbor 192.168.99.1 peer group WAN-OVERLAY-PEERS
   neighbor 192.168.99.1 description arista-pf1-ch-test
   neighbor 192.168.99.2 peer group WAN-OVERLAY-PEERS
   neighbor 192.168.99.2 description arista-pf2-ch-test
   redistribute connected route-map RM-CONN-2-BGP
   !
   address-family evpn
      neighbor WAN-OVERLAY-PEERS activate
   !
   address-family ipv4
      no neighbor WAN-OVERLAY-PEERS activate
      network 172.29.2.0/24
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
   vrf default
      rd 10.254.110.1:101
      route-target import evpn 65199:101
      route-target export evpn 65199:101
      route-target export evpn route-map RM-EVPN-EXPORT-VRF-DEFAULT
   !
   vrf prod
      rd 10.254.110.1:102
      route-target import evpn 65199:102
      route-target export evpn 65199:102
      router-id 10.254.110.1
      redistribute connected
```

## BFD

### Router BFD

#### Router BFD Multihop Summary

| Interval | Minimum RX | Multiplier |
| -------- | ---------- | ---------- |
| 300 | 300 | 3 |

#### Router BFD Device Configuration

```eos
!
router bfd
   multihop interval 300 min-rx 300 multiplier 3
```

## Filters

### Prefix-lists

#### Prefix-lists Summary

##### PL-LOOPBACKS-EVPN-OVERLAY

| Sequence | Action |
| -------- | ------ |
| 10 | permit 10.254.110.1/32 eq 32 |

##### PL-VRF-DEFAULT-NETWORKS

| Sequence | Action |
| -------- | ------ |
| 10 | permit 172.29.2.0/24 |

#### Prefix-lists Device Configuration

```eos
!
ip prefix-list PL-LOOPBACKS-EVPN-OVERLAY
   seq 10 permit 10.254.110.1/32 eq 32
!
ip prefix-list PL-VRF-DEFAULT-NETWORKS
   seq 10 permit 172.29.2.0/24
```

### Route-maps

#### Route-maps Summary

##### RM-CONN-2-BGP

| Sequence | Type | Match | Set | Sub-Route-Map | Continue |
| -------- | ---- | ----- | --- | ------------- | -------- |
| 10 | permit | ip address prefix-list PL-LOOPBACKS-EVPN-OVERLAY | - | - | - |

##### RM-EVPN-EXPORT-VRF-DEFAULT

| Sequence | Type | Match | Set | Sub-Route-Map | Continue |
| -------- | ---- | ----- | --- | ------------- | -------- |
| 30 | permit | ip address prefix-list PL-LOOPBACKS-EVPN-OVERLAY | - | - | - |
| 40 | permit | ip address prefix-list PL-VRF-DEFAULT-NETWORKS | - | - | - |

#### Route-maps Device Configuration

```eos
!
route-map RM-CONN-2-BGP permit 10
   match ip address prefix-list PL-LOOPBACKS-EVPN-OVERLAY
!
route-map RM-EVPN-EXPORT-VRF-DEFAULT permit 30
   match ip address prefix-list PL-LOOPBACKS-EVPN-OVERLAY
!
route-map RM-EVPN-EXPORT-VRF-DEFAULT permit 40
   match ip address prefix-list PL-VRF-DEFAULT-NETWORKS
```

## VRF Instances

### VRF Instances Summary

| VRF Name | IP Routing |
| -------- | ---------- |
| prod | enabled |

### VRF Instances Device Configuration

```eos
!
vrf instance prod
```

## System L1

### Unsupported Interface Configurations

| Unsupported Configuration | action |
| ---------------- | -------|
| Speed | error |
| Error correction | error |

### System L1 Device Configuration

```eos
!
system l1
   unsupported speed action error
   unsupported error-correction action error
```

## Application Traffic Recognition

### Applications

#### IPv4 Applications

| Name | Source Prefix | Destination Prefix | Protocols | Protocol Ranges | TCP Source Port Set | TCP Destination Port Set | UDP Source Port Set | UDP Destination Port Set |
| ---- | ------------- | ------------------ | --------- | --------------- | ------------------- | ------------------------ | ------------------- | ------------------------ |
| CONTROL-PLANE-APPLICATION | - | CONTROL-PLANE-APP-DEST-PREFIXES | - | - | - | - | - | - |

### Application Profiles

#### Application Profile Name CONTROL-PLANE-APPLICATION-PROFILE

| Type | Name | Service |
| ---- | ---- | ------- |
| application | CONTROL-PLANE-APPLICATION | - |

### Field Sets

#### IPv4 Prefix Sets

| Name | Prefixes |
| ---- | -------- |
| CONTROL-PLANE-APP-DEST-PREFIXES | 192.168.99.1/32<br>192.168.99.2/32 |

### Router Application-Traffic-Recognition Device Configuration

```eos
!
application traffic recognition
   !
   application ipv4 CONTROL-PLANE-APPLICATION
      destination prefix field-set CONTROL-PLANE-APP-DEST-PREFIXES
   !
   application-profile CONTROL-PLANE-APPLICATION-PROFILE
      application CONTROL-PLANE-APPLICATION
   !
   field-set ipv4 prefix CONTROL-PLANE-APP-DEST-PREFIXES
      192.168.99.1/32 192.168.99.2/32
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
| Path Group ID | 101 |
| IPSec profile | CP-PROFILE |

###### Local Interfaces

| Interface name | Public address | STUN server profile(s) |
| -------------- | -------------- | ---------------------- |
| Ethernet1 | - | internet-arista-pf1-ch-test-Ethernet1<br>internet-arista-pf2-ch-test-Ethernet1 |

###### Dynamic Peers Settings

| Setting | Value |
| ------  | ----- |
| IP Local | - |
| IPSec | - |

###### Static Peers

| Router IP | Name | IPv4 address(es) |
| --------- | ---- | ---------------- |
| 192.168.99.1 | arista-pf1-ch-test | 10.90.244.39 |
| 192.168.99.2 | arista-pf2-ch-test | 10.90.244.40 |

#### Load-balance Policies

| Policy Name | Jitter (ms) | Latency (ms) | Loss Rate (%) | Path Groups (priority) | Lowest Hop Count |
| ----------- | ----------- | ------------ | ------------- | ---------------------- | ---------------- |
| LB-CONTROL-PLANE-PROFILE | - | - | - | internet (1) | False |
| LB-DEFAULT-AVT-POLICY-DEFAULT | - | - | - | internet (1) | False |
| LB-DEFAULT-PROD-POLICY-DEFAULT | - | - | - | internet (1) | False |

#### Router Path-selection Device Configuration

```eos
!
router path-selection
   tcp mss ceiling ipv4 ingress
   !
   path-group internet id 101
      ipsec profile CP-PROFILE
      !
      local interface Ethernet1
         stun server-profile internet-arista-pf1-ch-test-Ethernet1 internet-arista-pf2-ch-test-Ethernet1
      !
      peer dynamic
      !
      peer static router-ip 192.168.99.1
         name arista-pf1-ch-test
         ipv4 address 10.90.244.39
      !
      peer static router-ip 192.168.99.2
         name arista-pf2-ch-test
         ipv4 address 10.90.244.40
   !
   load-balance policy LB-CONTROL-PLANE-PROFILE
      path-group internet
   !
   load-balance policy LB-DEFAULT-AVT-POLICY-DEFAULT
      path-group internet
   !
   load-balance policy LB-DEFAULT-PROD-POLICY-DEFAULT
      path-group internet
```

## STUN

### STUN Client

#### Server Profiles

| Server Profile | IP address | SSL Profile | Port |
| -------------- | ---------- | ----------- | ---- |
| internet-arista-pf1-ch-test-Ethernet1 | 10.90.244.39 | - | 3478 |
| internet-arista-pf2-ch-test-Ethernet1 | 10.90.244.40 | - | 3478 |

### STUN Device Configuration

```eos
!
stun
   client
      server-profile internet-arista-pf1-ch-test-Ethernet1
         ip address 10.90.244.39
      server-profile internet-arista-pf2-ch-test-Ethernet1
         ip address 10.90.244.40
```
