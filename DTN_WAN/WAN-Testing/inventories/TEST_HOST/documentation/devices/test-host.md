# test-host

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
- [Internal VLAN Allocation Policy](#internal-vlan-allocation-policy)
  - [Internal VLAN Allocation Policy Summary](#internal-vlan-allocation-policy-summary)
  - [Internal VLAN Allocation Policy Device Configuration](#internal-vlan-allocation-policy-device-configuration)
- [Interfaces](#interfaces)
  - [Ethernet Interfaces](#ethernet-interfaces)
  - [Loopback Interfaces](#loopback-interfaces)
- [Routing](#routing)
  - [Service Routing Protocols Model](#service-routing-protocols-model)
  - [IP Routing](#ip-routing)
  - [IPv6 Routing](#ipv6-routing)
  - [Static Routes](#static-routes)
- [Multicast](#multicast)
  - [IP IGMP Snooping](#ip-igmp-snooping)
- [VRF Instances](#vrf-instances)
  - [VRF Instances Summary](#vrf-instances-summary)
  - [VRF Instances Device Configuration](#vrf-instances-device-configuration)
- [EOS CLI Device Configuration](#eos-cli-device-configuration)

## Management

### Management Interfaces

#### Management Interfaces Summary

##### IPv4

| Management Interface | Description | Type | VRF | IP Address | Gateway |
| -------------------- | ----------- | ---- | --- | ---------- | ------- |
| Management0 | oob_management | oob | MGMT | 192.168.66.200/24 | 192.168.66.1 |

##### IPv6

| Management Interface | Description | Type | VRF | IPv6 Address | IPv6 Gateway |
| -------------------- | ----------- | ---- | --- | ------------ | ------------ |
| Management0 | oob_management | oob | MGMT | - | - |

#### Management Interfaces Device Configuration

```eos
!
interface Management0
   description oob_management
   no shutdown
   vrf MGMT
   ip address 192.168.66.200/24
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
| Management0 | MGMT |

##### NTP Servers

| Server | VRF | Preferred | Burst | iBurst | Version | Min Poll | Max Poll | Local-interface | Key |
| ------ | --- | --------- | ----- | ------ | ------- | -------- | -------- | --------------- | --- |
| 1.pool.ntp.org | MGMT | True | - | True | - | - | - | - | - |

#### NTP Device Configuration

```eos
!
ntp local-interface vrf MGMT Management0
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
| gzip | avd-cv-master-1.fun.aristanetworks.com:9910 | MGMT | token,/mnt/flash/token | ale,flexCounter,hardware,kni,pulse,strata | /Sysdb/cell/1/agent,/Sysdb/cell/2/agent | False |

#### TerminAttr Daemon Device Configuration

```eos
!
daemon TerminAttr
   exec /usr/bin/TerminAttr -cvaddr=avd-cv-master-1.fun.aristanetworks.com:9910 -cvauth=token,/mnt/flash/token -cvvrf=MGMT -smashexcludes=ale,flexCounter,hardware,kni,pulse,strata -ingestexclude=/Sysdb/cell/1/agent,/Sysdb/cell/2/agent -taillogs
   no shutdown
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

## Interfaces

### Ethernet Interfaces

#### Ethernet Interfaces Summary

##### L2

| Interface | Description | Mode | VLANs | Native VLAN | Trunk Group | Channel-Group |
| --------- | ----------- | ---- | ----- | ----------- | ----------- | ------------- |

*Inherited from Port-Channel Interface

##### Encapsulation Dot1q Interfaces

| Interface | Description | Type | Vlan ID | Dot1q VLAN Tag |
| --------- | ----------- | -----| ------- | -------------- |
| Ethernet1.10 | - | l3dot1q | - | 10 |
| Ethernet1.20 | - | l3dot1q | - | 20 |
| Ethernet2.10 | - | l3dot1q | - | 10 |
| Ethernet2.20 | - | l3dot1q | - | 20 |
| Ethernet3.10 | - | l3dot1q | - | 10 |
| Ethernet3.20 | - | l3dot1q | - | 20 |
| Ethernet4.10 | - | l3dot1q | - | 10 |
| Ethernet4.20 | - | l3dot1q | - | 20 |
| Ethernet5.10 | - | l3dot1q | - | 10 |
| Ethernet5.20 | - | l3dot1q | - | 20 |
| Ethernet6.10 | - | l3dot1q | - | 10 |
| Ethernet6.20 | - | l3dot1q | - | 20 |
| Ethernet7.10 | - | l3dot1q | - | 10 |
| Ethernet7.20 | - | l3dot1q | - | 20 |
| Ethernet8.10 | - | l3dot1q | - | 10 |
| Ethernet8.20 | - | l3dot1q | - | 20 |
| Ethernet9.10 | - | l3dot1q | - | 10 |
| Ethernet10.10 | - | l3dot1q | - | 10 |
| Ethernet10.20 | - | l3dot1q | - | 20 |
| Ethernet11.10 | - | l3dot1q | - | 10 |
| Ethernet11.20 | - | l3dot1q | - | 20 |

##### IPv4

| Interface | Description | Type | Channel Group | IP Address | VRF |  MTU | Shutdown | ACL In | ACL Out |
| --------- | ----------- | -----| ------------- | ---------- | ----| ---- | -------- | ------ | ------- |
| Ethernet1.10 | - | l3dot1q | - | 10.200.10.11/24 | dc1-guest-1 | - | False | - | - |
| Ethernet1.20 | - | l3dot1q | - | 10.200.20.11/24 | dc1-data-1 | - | False | - | - |
| Ethernet2.10 | - | l3dot1q | - | 10.200.10.12/24 | dc1-guest-2 | - | False | - | - |
| Ethernet2.20 | - | l3dot1q | - | 10.200.20.12/24 | dc1-data-2 | - | False | - | - |
| Ethernet3.10 | - | l3dot1q | - | 10.201.10.11/24 | site1-guest-1 | - | False | - | - |
| Ethernet3.20 | - | l3dot1q | - | 10.201.20.11/24 | site1-data-1 | - | False | - | - |
| Ethernet4.10 | - | l3dot1q | - | 10.201.10.12/24 | site1-guest-2 | - | False | - | - |
| Ethernet4.20 | - | l3dot1q | - | 10.201.20.12/24 | site1-data-2 | - | False | - | - |
| Ethernet5.10 | - | l3dot1q | - | 10.202.10.11/24 | site2-guest-1 | - | False | - | - |
| Ethernet5.20 | - | l3dot1q | - | 10.202.20.11/24 | site2-data-1 | - | False | - | - |
| Ethernet6.10 | - | l3dot1q | - | 10.202.10.12/24 | site2-guest-2 | - | False | - | - |
| Ethernet6.20 | - | l3dot1q | - | 10.202.20.12/24 | site2-data-2 | - | False | - | - |
| Ethernet7.10 | - | l3dot1q | - | 10.203.10.11/24 | site3-guest-1 | - | False | - | - |
| Ethernet7.20 | - | l3dot1q | - | 10.203.20.11/24 | site3-data-1 | - | False | - | - |
| Ethernet8.10 | - | l3dot1q | - | 10.203.10.12/24 | site3-guest-2 | - | False | - | - |
| Ethernet8.20 | - | l3dot1q | - | 10.203.20.12/24 | site3-data-2 | - | False | - | - |
| Ethernet9.10 | - | l3dot1q | - | 10.204.10.11/24 | site4-guest-1 | - | False | - | - |
| Ethernet10.10 | - | l3dot1q | - | 10.204.10.11/24 | site4-guest-2 | - | False | - | - |
| Ethernet10.20 | - | l3dot1q | - | 10.204.20.12/24 | site4-data-2 | - | False | - | - |
| Ethernet11.10 | - | l3dot1q | - | 10.205.10.11/24 | site5-guest-1 | - | False | - | - |
| Ethernet11.20 | - | l3dot1q | - | 10.205.20.11/24 | site5-data-1 | - | False | - | - |

#### Ethernet Interfaces Device Configuration

```eos
!
interface Ethernet1
   no shutdown
   no switchport
!
interface Ethernet1.10
   no shutdown
   encapsulation dot1q vlan 10
   vrf dc1-guest-1
   ip address 10.200.10.11/24
!
interface Ethernet1.20
   no shutdown
   encapsulation dot1q vlan 20
   vrf dc1-data-1
   ip address 10.200.20.11/24
!
interface Ethernet2
   no shutdown
   no switchport
!
interface Ethernet2.10
   no shutdown
   encapsulation dot1q vlan 10
   vrf dc1-guest-2
   ip address 10.200.10.12/24
!
interface Ethernet2.20
   no shutdown
   encapsulation dot1q vlan 20
   vrf dc1-data-2
   ip address 10.200.20.12/24
!
interface Ethernet3
   no shutdown
   no switchport
!
interface Ethernet3.10
   no shutdown
   encapsulation dot1q vlan 10
   vrf site1-guest-1
   ip address 10.201.10.11/24
!
interface Ethernet3.20
   no shutdown
   encapsulation dot1q vlan 20
   vrf site1-data-1
   ip address 10.201.20.11/24
!
interface Ethernet4
   no shutdown
   no switchport
!
interface Ethernet4.10
   no shutdown
   encapsulation dot1q vlan 10
   vrf site1-guest-2
   ip address 10.201.10.12/24
!
interface Ethernet4.20
   no shutdown
   encapsulation dot1q vlan 20
   vrf site1-data-2
   ip address 10.201.20.12/24
!
interface Ethernet5
   no shutdown
   no switchport
!
interface Ethernet5.10
   no shutdown
   encapsulation dot1q vlan 10
   vrf site2-guest-1
   ip address 10.202.10.11/24
!
interface Ethernet5.20
   no shutdown
   encapsulation dot1q vlan 20
   vrf site2-data-1
   ip address 10.202.20.11/24
!
interface Ethernet6
   no shutdown
   no switchport
!
interface Ethernet6.10
   no shutdown
   encapsulation dot1q vlan 10
   vrf site2-guest-2
   ip address 10.202.10.12/24
!
interface Ethernet6.20
   no shutdown
   encapsulation dot1q vlan 20
   vrf site2-data-2
   ip address 10.202.20.12/24
!
interface Ethernet7
   no shutdown
   no switchport
!
interface Ethernet7.10
   no shutdown
   encapsulation dot1q vlan 10
   vrf site3-guest-1
   ip address 10.203.10.11/24
!
interface Ethernet7.20
   no shutdown
   encapsulation dot1q vlan 20
   vrf site3-data-1
   ip address 10.203.20.11/24
!
interface Ethernet8
   no shutdown
   no switchport
!
interface Ethernet8.10
   no shutdown
   encapsulation dot1q vlan 10
   vrf site3-guest-2
   ip address 10.203.10.12/24
!
interface Ethernet8.20
   no shutdown
   encapsulation dot1q vlan 20
   vrf site3-data-2
   ip address 10.203.20.12/24
!
interface Ethernet9
   no shutdown
   no switchport
!
interface Ethernet9.10
   no shutdown
   encapsulation dot1q vlan 10
   vrf site4-guest-1
   ip address 10.204.10.11/24
!
interface Ethernet10
   no shutdown
   no switchport
!
interface Ethernet10.10
   no shutdown
   encapsulation dot1q vlan 10
   vrf site4-guest-2
   ip address 10.204.10.11/24
!
interface Ethernet10.20
   no shutdown
   encapsulation dot1q vlan 20
   vrf site4-data-2
   ip address 10.204.20.12/24
!
interface Ethernet11
   no shutdown
   no switchport
!
interface Ethernet11.10
   no shutdown
   encapsulation dot1q vlan 10
   vrf site5-guest-1
   ip address 10.205.10.11/24
!
interface Ethernet11.20
   no shutdown
   encapsulation dot1q vlan 20
   vrf site5-data-1
   ip address 10.205.20.11/24
```

### Loopback Interfaces

#### Loopback Interfaces Summary

##### IPv4

| Interface | Description | VRF | IP Address |
| --------- | ----------- | --- | ---------- |
| Loopback0 | Router_ID | default | 10.255.255.1/32 |

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
   ip address 10.255.255.1/32
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
| dc1-data-1 | True |
| dc1-data-2 | True |
| dc1-guest-1 | True |
| dc1-guest-2 | True |
| MGMT | False |
| site1-data-1 | True |
| site1-data-2 | True |
| site1-guest-1 | True |
| site1-guest-2 | True |
| site2-data-1 | True |
| site2-data-2 | True |
| site2-guest-1 | True |
| site2-guest-2 | True |
| site3-data-1 | True |
| site3-data-2 | True |
| site3-guest-1 | True |
| site3-guest-2 | True |
| site4-data-2 | True |
| site4-guest-1 | True |
| site4-guest-2 | True |
| site5-data-1 | True |
| site5-guest-1 | True |

#### IP Routing Device Configuration

```eos
!
ip routing
ip routing vrf dc1-data-1
ip routing vrf dc1-data-2
ip routing vrf dc1-guest-1
ip routing vrf dc1-guest-2
no ip routing vrf MGMT
ip routing vrf site1-data-1
ip routing vrf site1-data-2
ip routing vrf site1-guest-1
ip routing vrf site1-guest-2
ip routing vrf site2-data-1
ip routing vrf site2-data-2
ip routing vrf site2-guest-1
ip routing vrf site2-guest-2
ip routing vrf site3-data-1
ip routing vrf site3-data-2
ip routing vrf site3-guest-1
ip routing vrf site3-guest-2
ip routing vrf site4-data-2
ip routing vrf site4-guest-1
ip routing vrf site4-guest-2
ip routing vrf site5-data-1
ip routing vrf site5-guest-1
```

### IPv6 Routing

#### IPv6 Routing Summary

| VRF | Routing Enabled |
| --- | --------------- |
| default | False |
| dc1-data-1 | false |
| dc1-data-2 | false |
| dc1-guest-1 | false |
| dc1-guest-2 | false |
| MGMT | false |
| site1-data-1 | false |
| site1-data-2 | false |
| site1-guest-1 | false |
| site1-guest-2 | false |
| site2-data-1 | false |
| site2-data-2 | false |
| site2-guest-1 | false |
| site2-guest-2 | false |
| site3-data-1 | false |
| site3-data-2 | false |
| site3-guest-1 | false |
| site3-guest-2 | false |
| site4-data-2 | false |
| site4-guest-1 | false |
| site4-guest-2 | false |
| site5-data-1 | false |
| site5-guest-1 | false |

### Static Routes

#### Static Routes Summary

| VRF | Destination Prefix | Next Hop IP | Exit interface | Administrative Distance | Tag | Route Name | Metric |
| --- | ------------------ | ----------- | -------------- | ----------------------- | --- | ---------- | ------ |
| MGMT | 0.0.0.0/0 | 192.168.66.1 | - | 1 | - | - | - |
| dc1-data-1 | 0.0.0.0/0 | 10.200.20.1 | - | 1 | - | - | - |
| dc1-data-2 | 0.0.0.0/0 | 10.200.20.1 | - | 1 | - | - | - |
| dc1-guest-1 | 0.0.0.0/0 | 10.200.10.1 | - | 1 | - | - | - |
| dc1-guest-2 | 0.0.0.0/0 | 10.200.10.1 | - | 1 | - | - | - |
| site1-data-1 | 0.0.0.0/0 | 10.201.20.1 | - | 1 | - | - | - |
| site1-data-2 | 0.0.0.0/0 | 10.201.20.1 | - | 1 | - | - | - |
| site1-guest-1 | 0.0.0.0/0 | 10.201.10.1 | - | 1 | - | - | - |
| site1-guest-2 | 0.0.0.0/0 | 10.201.10.1 | - | 1 | - | - | - |
| site2-data-1 | 0.0.0.0/0 | 10.202.20.1 | - | 1 | - | - | - |
| site2-data-2 | 0.0.0.0/0 | 10.202.20.1 | - | 1 | - | - | - |
| site2-guest-1 | 0.0.0.0/0 | 10.202.10.1 | - | 1 | - | - | - |
| site2-guest-2 | 0.0.0.0/0 | 10.202.10.1 | - | 1 | - | - | - |
| site3-data-1 | 0.0.0.0/0 | 10.203.20.1 | - | 1 | - | - | - |
| site3-data-2 | 0.0.0.0/0 | 10.203.20.1 | - | 1 | - | - | - |
| site3-guest-1 | 0.0.0.0/0 | 10.203.10.1 | - | 1 | - | - | - |
| site3-guest-2 | 0.0.0.0/0 | 10.203.10.1 | - | 1 | - | - | - |
| site4-data-2 | 0.0.0.0/0 | 10.204.20.1 | - | 1 | - | - | - |
| site4-guest-1 | 0.0.0.0/0 | 10.204.10.1 | - | 1 | - | - | - |
| site4-guest-2 | 0.0.0.0/0 | 10.204.10.1 | - | 1 | - | - | - |
| site5-data-1 | 0.0.0.0/0 | 10.205.20.1 | - | 1 | - | - | - |
| site5-guest-1 | 0.0.0.0/0 | 10.205.10.1 | - | 1 | - | - | - |

#### Static Routes Device Configuration

```eos
!
ip route vrf MGMT 0.0.0.0/0 192.168.66.1
ip route vrf dc1-data-1 0.0.0.0/0 10.200.20.1
ip route vrf dc1-data-2 0.0.0.0/0 10.200.20.1
ip route vrf dc1-guest-1 0.0.0.0/0 10.200.10.1
ip route vrf dc1-guest-2 0.0.0.0/0 10.200.10.1
ip route vrf site1-data-1 0.0.0.0/0 10.201.20.1
ip route vrf site1-data-2 0.0.0.0/0 10.201.20.1
ip route vrf site1-guest-1 0.0.0.0/0 10.201.10.1
ip route vrf site1-guest-2 0.0.0.0/0 10.201.10.1
ip route vrf site2-data-1 0.0.0.0/0 10.202.20.1
ip route vrf site2-data-2 0.0.0.0/0 10.202.20.1
ip route vrf site2-guest-1 0.0.0.0/0 10.202.10.1
ip route vrf site2-guest-2 0.0.0.0/0 10.202.10.1
ip route vrf site3-data-1 0.0.0.0/0 10.203.20.1
ip route vrf site3-data-2 0.0.0.0/0 10.203.20.1
ip route vrf site3-guest-1 0.0.0.0/0 10.203.10.1
ip route vrf site3-guest-2 0.0.0.0/0 10.203.10.1
ip route vrf site4-data-2 0.0.0.0/0 10.204.20.1
ip route vrf site4-guest-1 0.0.0.0/0 10.204.10.1
ip route vrf site4-guest-2 0.0.0.0/0 10.204.10.1
ip route vrf site5-data-1 0.0.0.0/0 10.205.20.1
ip route vrf site5-guest-1 0.0.0.0/0 10.205.10.1
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

## VRF Instances

### VRF Instances Summary

| VRF Name | IP Routing |
| -------- | ---------- |
| dc1-data-1 | enabled |
| dc1-data-2 | enabled |
| dc1-guest-1 | enabled |
| dc1-guest-2 | enabled |
| MGMT | disabled |
| site1-data-1 | enabled |
| site1-data-2 | enabled |
| site1-guest-1 | enabled |
| site1-guest-2 | enabled |
| site2-data-1 | enabled |
| site2-data-2 | enabled |
| site2-guest-1 | enabled |
| site2-guest-2 | enabled |
| site3-data-1 | enabled |
| site3-data-2 | enabled |
| site3-guest-1 | enabled |
| site3-guest-2 | enabled |
| site4-data-2 | enabled |
| site4-guest-1 | enabled |
| site4-guest-2 | enabled |
| site5-data-1 | enabled |
| site5-guest-1 | enabled |

### VRF Instances Device Configuration

```eos
!
vrf instance dc1-data-1
!
vrf instance dc1-data-2
!
vrf instance dc1-guest-1
!
vrf instance dc1-guest-2
!
vrf instance MGMT
!
vrf instance site1-data-1
!
vrf instance site1-data-2
!
vrf instance site1-guest-1
!
vrf instance site1-guest-2
!
vrf instance site2-data-1
!
vrf instance site2-data-2
!
vrf instance site2-guest-1
!
vrf instance site2-guest-2
!
vrf instance site3-data-1
!
vrf instance site3-data-2
!
vrf instance site3-guest-1
!
vrf instance site3-guest-2
!
vrf instance site4-data-2
!
vrf instance site4-guest-1
!
vrf instance site4-guest-2
!
vrf instance site5-data-1
!
vrf instance site5-guest-1
```

## EOS CLI Device Configuration

```eos
!
platform tfa personality arfa

```
