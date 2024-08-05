# pf2

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
- [Platform](#platform)
  - [Platform Summary](#platform-summary)
  - [Platform Device Configuration](#platform-device-configuration)
- [Application Traffic Recognition](#application-traffic-recognition)
  - [Applications](#applications)
  - [Application Profiles](#application-profiles)
  - [Field Sets](#field-sets)
  - [Router Application-Traffic-Recognition Device Configuration](#router-application-traffic-recognition-device-configuration)
  - [Router Path-selection](#router-path-selection)
- [STUN](#stun)
  - [STUN Server](#stun-server)
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
| Management1/1 | oob_management | oob | MGMT | 172.28.137.186/17 | 172.28.128.1 |

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
   ip address 172.28.137.186/17
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
| CP-IKE-POLICY | - | - | - | 10.255.0.2 |

### Security Association policies

| Policy name | ESP Integrity | ESP Encryption | Lifetime | PFS DH Group |
| ----------- | ------------- | -------------- | -------- | ------------ |
| CP-SA-POLICY | - | aes256gcm128 | - | 14 |

### IPSec profiles

| Profile name | IKE policy | SA policy | Connection | DPD Interval | DPD Time | DPD action | Mode | Flow Parallelization |
| ------------ | ---------- | ----------| ---------- | ------------ | -------- | ---------- | ---- | -------------------- |
| CP-PROFILE | CP-IKE-POLICY | CP-SA-POLICY | start | - | - | - | transport | - |

### IP Security Device Configuration

```eos
!
ip security
   !
   ike policy CP-IKE-POLICY
      local-id 10.255.0.2
   !
   sa policy CP-SA-POLICY
      esp encryption aes256gcm128
      pfs dh-group 14
   !
   profile CP-PROFILE
      ike-policy CP-IKE-POLICY
      sa-policy CP-SA-POLICY
      connection start
      shared-key 7 <removed>
      dpd 10 50 clear
      mode transport
```

## Interfaces

### DPS Interfaces

#### DPS Interfaces Summary

| Interface | IP address | Shutdown | MTU | Flow tracker(s) | TCP MSS Ceiling |
| --------- | ---------- | -------- | --- | --------------- | --------------- |
| Dps1 | 10.255.0.2/32 | - | 9214 | Hardware: FLOW-TRACKER |  |

#### DPS Interfaces Device Configuration

```eos
!
interface Dps1
   description DPS Interface
   mtu 9214
   flow tracker hardware FLOW-TRACKER
   ip address 10.255.0.2/32
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
| Ethernet1/1 | gmpls1_PF-GMPLS-1 | routed | - | 172.16.52.2/30 | default | - | False | - | - |
| Ethernet1/2 | gmpls2_PF-GMPLS-2 | routed | - | 172.16.52.6/30 | default | - | False | - | - |
| Ethernet1/5 | rmpls1_PF-RMPLS-1 | routed | - | 172.16.52.10/30 | default | - | False | - | - |
| Ethernet1/6 | rmpls2_PF-RMPLS-2 | routed | - | 172.16.52.14/30 | default | - | False | - | - |
| Ethernet1/7 | rmpls3_PF-RMPLS-3 | routed | - | 172.16.52.18/30 | default | - | False | - | - |
| Ethernet1/8 | rmpls4_PF-RMPLS-4 | routed | - | 172.16.52.22/30 | default | - | False | - | - |

#### Ethernet Interfaces Device Configuration

```eos
!
interface Ethernet1/1
   description gmpls1_PF-GMPLS-1
   no shutdown
   no switchport
   ip address 172.16.52.2/30
!
interface Ethernet1/2
   description gmpls2_PF-GMPLS-2
   no shutdown
   no switchport
   ip address 172.16.52.6/30
!
interface Ethernet1/5
   description rmpls1_PF-RMPLS-1
   no shutdown
   no switchport
   ip address 172.16.52.10/30
!
interface Ethernet1/6
   description rmpls2_PF-RMPLS-2
   no shutdown
   no switchport
   ip address 172.16.52.14/30
!
interface Ethernet1/7
   description rmpls3_PF-RMPLS-3
   no shutdown
   no switchport
   ip address 172.16.52.18/30
!
interface Ethernet1/8
   description rmpls4_PF-RMPLS-4
   no shutdown
   no switchport
   ip address 172.16.52.22/30
```

### Loopback Interfaces

#### Loopback Interfaces Summary

##### IPv4

| Interface | Description | VRF | IP Address |
| --------- | ----------- | --- | ---------- |
| Loopback0 | Router_ID | default | 10.10.0.2/32 |

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
   ip address 10.10.0.2/32
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
| customer65 | 65 | - |
| customer66 | 66 | - |
| customer67 | 67 | - |
| customer68 | 68 | - |
| customer69 | 69 | - |
| customer70 | 70 | - |
| customer71 | 71 | - |
| customer72 | 72 | - |
| customer73 | 73 | - |
| customer74 | 74 | - |
| customer75 | 75 | - |
| customer76 | 76 | - |
| customer77 | 77 | - |
| customer78 | 78 | - |
| customer79 | 79 | - |
| customer80 | 80 | - |
| customer81 | 81 | - |
| customer82 | 82 | - |
| customer83 | 83 | - |
| customer84 | 84 | - |
| customer85 | 85 | - |
| customer86 | 86 | - |
| customer87 | 87 | - |
| customer88 | 88 | - |
| customer89 | 89 | - |
| customer90 | 90 | - |
| customer91 | 91 | - |
| customer92 | 92 | - |
| customer93 | 93 | - |
| customer94 | 94 | - |
| customer95 | 95 | - |
| customer96 | 96 | - |
| customer97 | 97 | - |
| customer98 | 98 | - |
| customer99 | 99 | - |
| customer100 | 100 | - |
| customer101 | 101 | - |
| customer102 | 102 | - |
| customer103 | 103 | - |
| customer104 | 104 | - |
| customer105 | 105 | - |
| customer106 | 106 | - |
| customer107 | 107 | - |
| customer108 | 108 | - |
| customer109 | 109 | - |
| customer110 | 110 | - |
| customer111 | 111 | - |
| customer112 | 112 | - |
| customer113 | 113 | - |
| customer114 | 114 | - |
| customer115 | 115 | - |
| customer116 | 116 | - |
| customer117 | 117 | - |
| customer118 | 118 | - |
| customer119 | 119 | - |
| customer120 | 120 | - |
| customer121 | 121 | - |
| customer122 | 122 | - |
| customer123 | 123 | - |
| customer124 | 124 | - |
| customer125 | 125 | - |
| customer126 | 126 | - |
| customer127 | 127 | - |
| customer128 | 128 | - |
| customer129 | 129 | - |
| customer130 | 130 | - |
| customer131 | 131 | - |
| customer132 | 132 | - |
| customer133 | 133 | - |
| customer134 | 134 | - |
| customer135 | 135 | - |
| customer136 | 136 | - |
| customer137 | 137 | - |
| customer138 | 138 | - |
| customer139 | 139 | - |
| customer140 | 140 | - |
| customer141 | 141 | - |
| customer142 | 142 | - |
| customer143 | 143 | - |
| customer144 | 144 | - |
| customer145 | 145 | - |
| customer146 | 146 | - |
| customer147 | 147 | - |
| customer148 | 148 | - |
| customer149 | 149 | - |
| customer150 | 150 | - |
| customer151 | 151 | - |
| customer152 | 152 | - |
| customer153 | 153 | - |
| customer154 | 154 | - |
| customer155 | 155 | - |
| customer156 | 156 | - |
| customer157 | 157 | - |
| customer158 | 158 | - |
| customer159 | 159 | - |
| customer160 | 160 | - |
| customer161 | 161 | - |
| customer162 | 162 | - |
| customer163 | 163 | - |
| customer164 | 164 | - |
| customer165 | 165 | - |
| customer166 | 166 | - |
| customer167 | 167 | - |
| customer168 | 168 | - |
| customer169 | 169 | - |
| customer170 | 170 | - |
| customer171 | 171 | - |
| customer172 | 172 | - |
| customer173 | 173 | - |
| customer174 | 174 | - |
| customer175 | 175 | - |
| customer176 | 176 | - |
| customer177 | 177 | - |
| customer178 | 178 | - |
| customer179 | 179 | - |
| customer180 | 180 | - |
| customer181 | 181 | - |
| customer182 | 182 | - |
| customer183 | 183 | - |
| customer184 | 184 | - |
| customer185 | 185 | - |
| customer186 | 186 | - |
| customer187 | 187 | - |
| customer188 | 188 | - |
| customer189 | 189 | - |
| customer190 | 190 | - |
| customer191 | 191 | - |
| customer192 | 192 | - |
| customer193 | 193 | - |
| customer194 | 194 | - |
| customer195 | 195 | - |
| customer196 | 196 | - |
| customer197 | 197 | - |
| customer198 | 198 | - |
| customer199 | 199 | - |
| customer200 | 200 | - |
| customer201 | 201 | - |
| customer202 | 202 | - |
| customer203 | 203 | - |
| customer204 | 204 | - |
| customer205 | 205 | - |
| customer206 | 206 | - |
| customer207 | 207 | - |
| customer208 | 208 | - |
| customer209 | 209 | - |
| customer210 | 210 | - |
| customer211 | 211 | - |
| customer212 | 212 | - |
| customer213 | 213 | - |
| customer214 | 214 | - |
| customer215 | 215 | - |
| customer216 | 216 | - |
| customer217 | 217 | - |
| customer218 | 218 | - |
| customer219 | 219 | - |
| customer220 | 220 | - |
| customer221 | 221 | - |
| customer222 | 222 | - |
| customer223 | 223 | - |
| customer224 | 224 | - |
| customer225 | 225 | - |
| customer226 | 226 | - |
| customer227 | 227 | - |
| customer228 | 228 | - |
| customer229 | 229 | - |
| customer230 | 230 | - |
| customer231 | 231 | - |
| customer232 | 232 | - |
| customer233 | 233 | - |
| customer234 | 234 | - |
| customer235 | 235 | - |
| customer236 | 236 | - |
| customer237 | 237 | - |
| customer238 | 238 | - |
| customer239 | 239 | - |
| customer240 | 240 | - |
| customer241 | 241 | - |
| customer242 | 242 | - |
| customer243 | 243 | - |
| customer244 | 244 | - |
| customer245 | 245 | - |
| customer246 | 246 | - |
| customer247 | 247 | - |
| customer248 | 248 | - |
| customer249 | 249 | - |
| customer250 | 250 | - |
| customer251 | 251 | - |
| customer252 | 252 | - |
| customer253 | 253 | - |
| default | 1 | - |

#### VXLAN Interface Device Configuration

```eos
!
interface Vxlan1
   description pf2_VTEP
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
   vxlan vrf customer65 vni 65
   vxlan vrf customer66 vni 66
   vxlan vrf customer67 vni 67
   vxlan vrf customer68 vni 68
   vxlan vrf customer69 vni 69
   vxlan vrf customer70 vni 70
   vxlan vrf customer71 vni 71
   vxlan vrf customer72 vni 72
   vxlan vrf customer73 vni 73
   vxlan vrf customer74 vni 74
   vxlan vrf customer75 vni 75
   vxlan vrf customer76 vni 76
   vxlan vrf customer77 vni 77
   vxlan vrf customer78 vni 78
   vxlan vrf customer79 vni 79
   vxlan vrf customer80 vni 80
   vxlan vrf customer81 vni 81
   vxlan vrf customer82 vni 82
   vxlan vrf customer83 vni 83
   vxlan vrf customer84 vni 84
   vxlan vrf customer85 vni 85
   vxlan vrf customer86 vni 86
   vxlan vrf customer87 vni 87
   vxlan vrf customer88 vni 88
   vxlan vrf customer89 vni 89
   vxlan vrf customer90 vni 90
   vxlan vrf customer91 vni 91
   vxlan vrf customer92 vni 92
   vxlan vrf customer93 vni 93
   vxlan vrf customer94 vni 94
   vxlan vrf customer95 vni 95
   vxlan vrf customer96 vni 96
   vxlan vrf customer97 vni 97
   vxlan vrf customer98 vni 98
   vxlan vrf customer99 vni 99
   vxlan vrf customer100 vni 100
   vxlan vrf customer101 vni 101
   vxlan vrf customer102 vni 102
   vxlan vrf customer103 vni 103
   vxlan vrf customer104 vni 104
   vxlan vrf customer105 vni 105
   vxlan vrf customer106 vni 106
   vxlan vrf customer107 vni 107
   vxlan vrf customer108 vni 108
   vxlan vrf customer109 vni 109
   vxlan vrf customer110 vni 110
   vxlan vrf customer111 vni 111
   vxlan vrf customer112 vni 112
   vxlan vrf customer113 vni 113
   vxlan vrf customer114 vni 114
   vxlan vrf customer115 vni 115
   vxlan vrf customer116 vni 116
   vxlan vrf customer117 vni 117
   vxlan vrf customer118 vni 118
   vxlan vrf customer119 vni 119
   vxlan vrf customer120 vni 120
   vxlan vrf customer121 vni 121
   vxlan vrf customer122 vni 122
   vxlan vrf customer123 vni 123
   vxlan vrf customer124 vni 124
   vxlan vrf customer125 vni 125
   vxlan vrf customer126 vni 126
   vxlan vrf customer127 vni 127
   vxlan vrf customer128 vni 128
   vxlan vrf customer129 vni 129
   vxlan vrf customer130 vni 130
   vxlan vrf customer131 vni 131
   vxlan vrf customer132 vni 132
   vxlan vrf customer133 vni 133
   vxlan vrf customer134 vni 134
   vxlan vrf customer135 vni 135
   vxlan vrf customer136 vni 136
   vxlan vrf customer137 vni 137
   vxlan vrf customer138 vni 138
   vxlan vrf customer139 vni 139
   vxlan vrf customer140 vni 140
   vxlan vrf customer141 vni 141
   vxlan vrf customer142 vni 142
   vxlan vrf customer143 vni 143
   vxlan vrf customer144 vni 144
   vxlan vrf customer145 vni 145
   vxlan vrf customer146 vni 146
   vxlan vrf customer147 vni 147
   vxlan vrf customer148 vni 148
   vxlan vrf customer149 vni 149
   vxlan vrf customer150 vni 150
   vxlan vrf customer151 vni 151
   vxlan vrf customer152 vni 152
   vxlan vrf customer153 vni 153
   vxlan vrf customer154 vni 154
   vxlan vrf customer155 vni 155
   vxlan vrf customer156 vni 156
   vxlan vrf customer157 vni 157
   vxlan vrf customer158 vni 158
   vxlan vrf customer159 vni 159
   vxlan vrf customer160 vni 160
   vxlan vrf customer161 vni 161
   vxlan vrf customer162 vni 162
   vxlan vrf customer163 vni 163
   vxlan vrf customer164 vni 164
   vxlan vrf customer165 vni 165
   vxlan vrf customer166 vni 166
   vxlan vrf customer167 vni 167
   vxlan vrf customer168 vni 168
   vxlan vrf customer169 vni 169
   vxlan vrf customer170 vni 170
   vxlan vrf customer171 vni 171
   vxlan vrf customer172 vni 172
   vxlan vrf customer173 vni 173
   vxlan vrf customer174 vni 174
   vxlan vrf customer175 vni 175
   vxlan vrf customer176 vni 176
   vxlan vrf customer177 vni 177
   vxlan vrf customer178 vni 178
   vxlan vrf customer179 vni 179
   vxlan vrf customer180 vni 180
   vxlan vrf customer181 vni 181
   vxlan vrf customer182 vni 182
   vxlan vrf customer183 vni 183
   vxlan vrf customer184 vni 184
   vxlan vrf customer185 vni 185
   vxlan vrf customer186 vni 186
   vxlan vrf customer187 vni 187
   vxlan vrf customer188 vni 188
   vxlan vrf customer189 vni 189
   vxlan vrf customer190 vni 190
   vxlan vrf customer191 vni 191
   vxlan vrf customer192 vni 192
   vxlan vrf customer193 vni 193
   vxlan vrf customer194 vni 194
   vxlan vrf customer195 vni 195
   vxlan vrf customer196 vni 196
   vxlan vrf customer197 vni 197
   vxlan vrf customer198 vni 198
   vxlan vrf customer199 vni 199
   vxlan vrf customer200 vni 200
   vxlan vrf customer201 vni 201
   vxlan vrf customer202 vni 202
   vxlan vrf customer203 vni 203
   vxlan vrf customer204 vni 204
   vxlan vrf customer205 vni 205
   vxlan vrf customer206 vni 206
   vxlan vrf customer207 vni 207
   vxlan vrf customer208 vni 208
   vxlan vrf customer209 vni 209
   vxlan vrf customer210 vni 210
   vxlan vrf customer211 vni 211
   vxlan vrf customer212 vni 212
   vxlan vrf customer213 vni 213
   vxlan vrf customer214 vni 214
   vxlan vrf customer215 vni 215
   vxlan vrf customer216 vni 216
   vxlan vrf customer217 vni 217
   vxlan vrf customer218 vni 218
   vxlan vrf customer219 vni 219
   vxlan vrf customer220 vni 220
   vxlan vrf customer221 vni 221
   vxlan vrf customer222 vni 222
   vxlan vrf customer223 vni 223
   vxlan vrf customer224 vni 224
   vxlan vrf customer225 vni 225
   vxlan vrf customer226 vni 226
   vxlan vrf customer227 vni 227
   vxlan vrf customer228 vni 228
   vxlan vrf customer229 vni 229
   vxlan vrf customer230 vni 230
   vxlan vrf customer231 vni 231
   vxlan vrf customer232 vni 232
   vxlan vrf customer233 vni 233
   vxlan vrf customer234 vni 234
   vxlan vrf customer235 vni 235
   vxlan vrf customer236 vni 236
   vxlan vrf customer237 vni 237
   vxlan vrf customer238 vni 238
   vxlan vrf customer239 vni 239
   vxlan vrf customer240 vni 240
   vxlan vrf customer241 vni 241
   vxlan vrf customer242 vni 242
   vxlan vrf customer243 vni 243
   vxlan vrf customer244 vni 244
   vxlan vrf customer245 vni 245
   vxlan vrf customer246 vni 246
   vxlan vrf customer247 vni 247
   vxlan vrf customer248 vni 248
   vxlan vrf customer249 vni 249
   vxlan vrf customer250 vni 250
   vxlan vrf customer251 vni 251
   vxlan vrf customer252 vni 252
   vxlan vrf customer253 vni 253
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
| MGMT | False |

#### IP Routing Device Configuration

```eos
!
ip routing
no ip routing vrf MGMT
```

### IPv6 Routing

#### IPv6 Routing Summary

| VRF | Routing Enabled |
| --- | --------------- |
| default | False |
| MGMT | false |

### Static Routes

#### Static Routes Summary

| VRF | Destination Prefix | Next Hop IP | Exit interface | Administrative Distance | Tag | Route Name | Metric |
| --- | ------------------ | ----------- | -------------- | ----------------------- | --- | ---------- | ------ |
| MGMT | 0.0.0.0/0 | 172.28.128.1 | - | 1 | - | - | - |
| default | 172.16.51.0/30 | 172.16.52.1 | - | 1 | - | - | - |
| default | 172.16.52.0/30 | 172.16.52.1 | - | 1 | - | - | - |
| default | 172.16.151.0/30 | 172.16.52.1 | - | 1 | - | - | - |
| default | 172.16.201.0/30 | 172.16.52.1 | - | 1 | - | - | - |
| default | 172.16.51.4/30 | 172.16.52.5 | - | 1 | - | - | - |
| default | 172.16.52.4/30 | 172.16.52.5 | - | 1 | - | - | - |
| default | 172.16.152.0/30 | 172.16.52.5 | - | 1 | - | - | - |
| default | 172.16.202.0/30 | 172.16.52.5 | - | 1 | - | - | - |
| default | 172.16.51.8/30 | 172.16.52.9 | - | 1 | - | - | - |
| default | 172.16.52.8/30 | 172.16.52.9 | - | 1 | - | - | - |
| default | 172.16.203.0/30 | 172.16.52.9 | - | 1 | - | - | - |
| default | 172.16.211.0/30 | 172.16.52.9 | - | 1 | - | - | - |
| default | 172.16.221.0/30 | 172.16.52.9 | - | 1 | - | - | - |
| default | 172.16.51.12/30 | 172.16.52.13 | - | 1 | - | - | - |
| default | 172.16.52.12/30 | 172.16.52.13 | - | 1 | - | - | - |
| default | 172.16.204.0/30 | 172.16.52.13 | - | 1 | - | - | - |
| default | 172.16.212.0/30 | 172.16.52.13 | - | 1 | - | - | - |
| default | 172.16.222.0/30 | 172.16.52.13 | - | 1 | - | - | - |
| default | 172.16.51.16/30 | 172.16.52.17 | - | 1 | - | - | - |
| default | 172.16.52.16/30 | 172.16.52.17 | - | 1 | - | - | - |
| default | 172.16.103.0/30 | 172.16.52.17 | - | 1 | - | - | - |
| default | 172.16.113.0/30 | 172.16.52.17 | - | 1 | - | - | - |
| default | 172.16.123.0/30 | 172.16.52.17 | - | 1 | - | - | - |
| default | 172.16.51.20/30 | 172.16.52.21 | - | 1 | - | - | - |
| default | 172.16.52.20/30 | 172.16.52.21 | - | 1 | - | - | - |
| default | 172.16.104.0/30 | 172.16.52.21 | - | 1 | - | - | - |
| default | 172.16.114.0/30 | 172.16.52.21 | - | 1 | - | - | - |
| default | 172.16.124.0/30 | 172.16.52.21 | - | 1 | - | - | - |

#### Static Routes Device Configuration

```eos
!
ip route vrf MGMT 0.0.0.0/0 172.28.128.1
ip route 172.16.51.0/30 172.16.52.1
ip route 172.16.52.0/30 172.16.52.1
ip route 172.16.151.0/30 172.16.52.1
ip route 172.16.201.0/30 172.16.52.1
ip route 172.16.51.4/30 172.16.52.5
ip route 172.16.52.4/30 172.16.52.5
ip route 172.16.152.0/30 172.16.52.5
ip route 172.16.202.0/30 172.16.52.5
ip route 172.16.51.8/30 172.16.52.9
ip route 172.16.52.8/30 172.16.52.9
ip route 172.16.203.0/30 172.16.52.9
ip route 172.16.211.0/30 172.16.52.9
ip route 172.16.221.0/30 172.16.52.9
ip route 172.16.51.12/30 172.16.52.13
ip route 172.16.52.12/30 172.16.52.13
ip route 172.16.204.0/30 172.16.52.13
ip route 172.16.212.0/30 172.16.52.13
ip route 172.16.222.0/30 172.16.52.13
ip route 172.16.51.16/30 172.16.52.17
ip route 172.16.52.16/30 172.16.52.17
ip route 172.16.103.0/30 172.16.52.17
ip route 172.16.113.0/30 172.16.52.17
ip route 172.16.123.0/30 172.16.52.17
ip route 172.16.51.20/30 172.16.52.21
ip route 172.16.52.20/30 172.16.52.21
ip route 172.16.104.0/30 172.16.52.21
ip route 172.16.114.0/30 172.16.52.21
ip route 172.16.124.0/30 172.16.52.21
```

### Router Adaptive Virtual Topology

#### Router Adaptive Virtual Topology Summary

Topology role: pathfinder

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

##### VRF customer65

| AVT policy |
| ---------- |
| TELEPRES-AVT-POLICY |

| AVT Profile | AVT ID |
| ----------- | ------ |
| TELEPRES-AVT-POLICY-DEFAULT | 1 |
| TELEPRES-AVT-POLICY-VOICE | 2 |

##### VRF customer66

| AVT policy |
| ---------- |
| TELEPRES-AVT-POLICY |

| AVT Profile | AVT ID |
| ----------- | ------ |
| TELEPRES-AVT-POLICY-DEFAULT | 1 |
| TELEPRES-AVT-POLICY-VOICE | 2 |

##### VRF customer67

| AVT policy |
| ---------- |
| TELEPRES-AVT-POLICY |

| AVT Profile | AVT ID |
| ----------- | ------ |
| TELEPRES-AVT-POLICY-DEFAULT | 1 |
| TELEPRES-AVT-POLICY-VOICE | 2 |

##### VRF customer68

| AVT policy |
| ---------- |
| TELEPRES-AVT-POLICY |

| AVT Profile | AVT ID |
| ----------- | ------ |
| TELEPRES-AVT-POLICY-DEFAULT | 1 |
| TELEPRES-AVT-POLICY-VOICE | 2 |

##### VRF customer69

| AVT policy |
| ---------- |
| TELEPRES-AVT-POLICY |

| AVT Profile | AVT ID |
| ----------- | ------ |
| TELEPRES-AVT-POLICY-DEFAULT | 1 |
| TELEPRES-AVT-POLICY-VOICE | 2 |

##### VRF customer70

| AVT policy |
| ---------- |
| TELEPRES-AVT-POLICY |

| AVT Profile | AVT ID |
| ----------- | ------ |
| TELEPRES-AVT-POLICY-DEFAULT | 1 |
| TELEPRES-AVT-POLICY-VOICE | 2 |

##### VRF customer71

| AVT policy |
| ---------- |
| TELEPRES-AVT-POLICY |

| AVT Profile | AVT ID |
| ----------- | ------ |
| TELEPRES-AVT-POLICY-DEFAULT | 1 |
| TELEPRES-AVT-POLICY-VOICE | 2 |

##### VRF customer72

| AVT policy |
| ---------- |
| TELEPRES-AVT-POLICY |

| AVT Profile | AVT ID |
| ----------- | ------ |
| TELEPRES-AVT-POLICY-DEFAULT | 1 |
| TELEPRES-AVT-POLICY-VOICE | 2 |

##### VRF customer73

| AVT policy |
| ---------- |
| TELEPRES-AVT-POLICY |

| AVT Profile | AVT ID |
| ----------- | ------ |
| TELEPRES-AVT-POLICY-DEFAULT | 1 |
| TELEPRES-AVT-POLICY-VOICE | 2 |

##### VRF customer74

| AVT policy |
| ---------- |
| TELEPRES-AVT-POLICY |

| AVT Profile | AVT ID |
| ----------- | ------ |
| TELEPRES-AVT-POLICY-DEFAULT | 1 |
| TELEPRES-AVT-POLICY-VOICE | 2 |

##### VRF customer75

| AVT policy |
| ---------- |
| TELEPRES-AVT-POLICY |

| AVT Profile | AVT ID |
| ----------- | ------ |
| TELEPRES-AVT-POLICY-DEFAULT | 1 |
| TELEPRES-AVT-POLICY-VOICE | 2 |

##### VRF customer76

| AVT policy |
| ---------- |
| TELEPRES-AVT-POLICY |

| AVT Profile | AVT ID |
| ----------- | ------ |
| TELEPRES-AVT-POLICY-DEFAULT | 1 |
| TELEPRES-AVT-POLICY-VOICE | 2 |

##### VRF customer77

| AVT policy |
| ---------- |
| TELEPRES-AVT-POLICY |

| AVT Profile | AVT ID |
| ----------- | ------ |
| TELEPRES-AVT-POLICY-DEFAULT | 1 |
| TELEPRES-AVT-POLICY-VOICE | 2 |

##### VRF customer78

| AVT policy |
| ---------- |
| TELEPRES-AVT-POLICY |

| AVT Profile | AVT ID |
| ----------- | ------ |
| TELEPRES-AVT-POLICY-DEFAULT | 1 |
| TELEPRES-AVT-POLICY-VOICE | 2 |

##### VRF customer79

| AVT policy |
| ---------- |
| TELEPRES-AVT-POLICY |

| AVT Profile | AVT ID |
| ----------- | ------ |
| TELEPRES-AVT-POLICY-DEFAULT | 1 |
| TELEPRES-AVT-POLICY-VOICE | 2 |

##### VRF customer80

| AVT policy |
| ---------- |
| TELEPRES-AVT-POLICY |

| AVT Profile | AVT ID |
| ----------- | ------ |
| TELEPRES-AVT-POLICY-DEFAULT | 1 |
| TELEPRES-AVT-POLICY-VOICE | 2 |

##### VRF customer81

| AVT policy |
| ---------- |
| TELEPRES-AVT-POLICY |

| AVT Profile | AVT ID |
| ----------- | ------ |
| TELEPRES-AVT-POLICY-DEFAULT | 1 |
| TELEPRES-AVT-POLICY-VOICE | 2 |

##### VRF customer82

| AVT policy |
| ---------- |
| TELEPRES-AVT-POLICY |

| AVT Profile | AVT ID |
| ----------- | ------ |
| TELEPRES-AVT-POLICY-DEFAULT | 1 |
| TELEPRES-AVT-POLICY-VOICE | 2 |

##### VRF customer83

| AVT policy |
| ---------- |
| TELEPRES-AVT-POLICY |

| AVT Profile | AVT ID |
| ----------- | ------ |
| TELEPRES-AVT-POLICY-DEFAULT | 1 |
| TELEPRES-AVT-POLICY-VOICE | 2 |

##### VRF customer84

| AVT policy |
| ---------- |
| TELEPRES-AVT-POLICY |

| AVT Profile | AVT ID |
| ----------- | ------ |
| TELEPRES-AVT-POLICY-DEFAULT | 1 |
| TELEPRES-AVT-POLICY-VOICE | 2 |

##### VRF customer85

| AVT policy |
| ---------- |
| TELEPRES-AVT-POLICY |

| AVT Profile | AVT ID |
| ----------- | ------ |
| TELEPRES-AVT-POLICY-DEFAULT | 1 |
| TELEPRES-AVT-POLICY-VOICE | 2 |

##### VRF customer86

| AVT policy |
| ---------- |
| TELEPRES-AVT-POLICY |

| AVT Profile | AVT ID |
| ----------- | ------ |
| TELEPRES-AVT-POLICY-DEFAULT | 1 |
| TELEPRES-AVT-POLICY-VOICE | 2 |

##### VRF customer87

| AVT policy |
| ---------- |
| TELEPRES-AVT-POLICY |

| AVT Profile | AVT ID |
| ----------- | ------ |
| TELEPRES-AVT-POLICY-DEFAULT | 1 |
| TELEPRES-AVT-POLICY-VOICE | 2 |

##### VRF customer88

| AVT policy |
| ---------- |
| TELEPRES-AVT-POLICY |

| AVT Profile | AVT ID |
| ----------- | ------ |
| TELEPRES-AVT-POLICY-DEFAULT | 1 |
| TELEPRES-AVT-POLICY-VOICE | 2 |

##### VRF customer89

| AVT policy |
| ---------- |
| TELEPRES-AVT-POLICY |

| AVT Profile | AVT ID |
| ----------- | ------ |
| TELEPRES-AVT-POLICY-DEFAULT | 1 |
| TELEPRES-AVT-POLICY-VOICE | 2 |

##### VRF customer90

| AVT policy |
| ---------- |
| TELEPRES-AVT-POLICY |

| AVT Profile | AVT ID |
| ----------- | ------ |
| TELEPRES-AVT-POLICY-DEFAULT | 1 |
| TELEPRES-AVT-POLICY-VOICE | 2 |

##### VRF customer91

| AVT policy |
| ---------- |
| TELEPRES-AVT-POLICY |

| AVT Profile | AVT ID |
| ----------- | ------ |
| TELEPRES-AVT-POLICY-DEFAULT | 1 |
| TELEPRES-AVT-POLICY-VOICE | 2 |

##### VRF customer92

| AVT policy |
| ---------- |
| TELEPRES-AVT-POLICY |

| AVT Profile | AVT ID |
| ----------- | ------ |
| TELEPRES-AVT-POLICY-DEFAULT | 1 |
| TELEPRES-AVT-POLICY-VOICE | 2 |

##### VRF customer93

| AVT policy |
| ---------- |
| TELEPRES-AVT-POLICY |

| AVT Profile | AVT ID |
| ----------- | ------ |
| TELEPRES-AVT-POLICY-DEFAULT | 1 |
| TELEPRES-AVT-POLICY-VOICE | 2 |

##### VRF customer94

| AVT policy |
| ---------- |
| TELEPRES-AVT-POLICY |

| AVT Profile | AVT ID |
| ----------- | ------ |
| TELEPRES-AVT-POLICY-DEFAULT | 1 |
| TELEPRES-AVT-POLICY-VOICE | 2 |

##### VRF customer95

| AVT policy |
| ---------- |
| TELEPRES-AVT-POLICY |

| AVT Profile | AVT ID |
| ----------- | ------ |
| TELEPRES-AVT-POLICY-DEFAULT | 1 |
| TELEPRES-AVT-POLICY-VOICE | 2 |

##### VRF customer96

| AVT policy |
| ---------- |
| TELEPRES-AVT-POLICY |

| AVT Profile | AVT ID |
| ----------- | ------ |
| TELEPRES-AVT-POLICY-DEFAULT | 1 |
| TELEPRES-AVT-POLICY-VOICE | 2 |

##### VRF customer97

| AVT policy |
| ---------- |
| TELEPRES-AVT-POLICY |

| AVT Profile | AVT ID |
| ----------- | ------ |
| TELEPRES-AVT-POLICY-DEFAULT | 1 |
| TELEPRES-AVT-POLICY-VOICE | 2 |

##### VRF customer98

| AVT policy |
| ---------- |
| TELEPRES-AVT-POLICY |

| AVT Profile | AVT ID |
| ----------- | ------ |
| TELEPRES-AVT-POLICY-DEFAULT | 1 |
| TELEPRES-AVT-POLICY-VOICE | 2 |

##### VRF customer99

| AVT policy |
| ---------- |
| TELEPRES-AVT-POLICY |

| AVT Profile | AVT ID |
| ----------- | ------ |
| TELEPRES-AVT-POLICY-DEFAULT | 1 |
| TELEPRES-AVT-POLICY-VOICE | 2 |

##### VRF customer100

| AVT policy |
| ---------- |
| TELEPRES-AVT-POLICY |

| AVT Profile | AVT ID |
| ----------- | ------ |
| TELEPRES-AVT-POLICY-DEFAULT | 1 |
| TELEPRES-AVT-POLICY-VOICE | 2 |

##### VRF customer101

| AVT policy |
| ---------- |
| TELEPRES-AVT-POLICY |

| AVT Profile | AVT ID |
| ----------- | ------ |
| TELEPRES-AVT-POLICY-DEFAULT | 1 |
| TELEPRES-AVT-POLICY-VOICE | 2 |

##### VRF customer102

| AVT policy |
| ---------- |
| TELEPRES-AVT-POLICY |

| AVT Profile | AVT ID |
| ----------- | ------ |
| TELEPRES-AVT-POLICY-DEFAULT | 1 |
| TELEPRES-AVT-POLICY-VOICE | 2 |

##### VRF customer103

| AVT policy |
| ---------- |
| TELEPRES-AVT-POLICY |

| AVT Profile | AVT ID |
| ----------- | ------ |
| TELEPRES-AVT-POLICY-DEFAULT | 1 |
| TELEPRES-AVT-POLICY-VOICE | 2 |

##### VRF customer104

| AVT policy |
| ---------- |
| TELEPRES-AVT-POLICY |

| AVT Profile | AVT ID |
| ----------- | ------ |
| TELEPRES-AVT-POLICY-DEFAULT | 1 |
| TELEPRES-AVT-POLICY-VOICE | 2 |

##### VRF customer105

| AVT policy |
| ---------- |
| TELEPRES-AVT-POLICY |

| AVT Profile | AVT ID |
| ----------- | ------ |
| TELEPRES-AVT-POLICY-DEFAULT | 1 |
| TELEPRES-AVT-POLICY-VOICE | 2 |

##### VRF customer106

| AVT policy |
| ---------- |
| TELEPRES-AVT-POLICY |

| AVT Profile | AVT ID |
| ----------- | ------ |
| TELEPRES-AVT-POLICY-DEFAULT | 1 |
| TELEPRES-AVT-POLICY-VOICE | 2 |

##### VRF customer107

| AVT policy |
| ---------- |
| TELEPRES-AVT-POLICY |

| AVT Profile | AVT ID |
| ----------- | ------ |
| TELEPRES-AVT-POLICY-DEFAULT | 1 |
| TELEPRES-AVT-POLICY-VOICE | 2 |

##### VRF customer108

| AVT policy |
| ---------- |
| TELEPRES-AVT-POLICY |

| AVT Profile | AVT ID |
| ----------- | ------ |
| TELEPRES-AVT-POLICY-DEFAULT | 1 |
| TELEPRES-AVT-POLICY-VOICE | 2 |

##### VRF customer109

| AVT policy |
| ---------- |
| TELEPRES-AVT-POLICY |

| AVT Profile | AVT ID |
| ----------- | ------ |
| TELEPRES-AVT-POLICY-DEFAULT | 1 |
| TELEPRES-AVT-POLICY-VOICE | 2 |

##### VRF customer110

| AVT policy |
| ---------- |
| TELEPRES-AVT-POLICY |

| AVT Profile | AVT ID |
| ----------- | ------ |
| TELEPRES-AVT-POLICY-DEFAULT | 1 |
| TELEPRES-AVT-POLICY-VOICE | 2 |

##### VRF customer111

| AVT policy |
| ---------- |
| TELEPRES-AVT-POLICY |

| AVT Profile | AVT ID |
| ----------- | ------ |
| TELEPRES-AVT-POLICY-DEFAULT | 1 |
| TELEPRES-AVT-POLICY-VOICE | 2 |

##### VRF customer112

| AVT policy |
| ---------- |
| TELEPRES-AVT-POLICY |

| AVT Profile | AVT ID |
| ----------- | ------ |
| TELEPRES-AVT-POLICY-DEFAULT | 1 |
| TELEPRES-AVT-POLICY-VOICE | 2 |

##### VRF customer113

| AVT policy |
| ---------- |
| TELEPRES-AVT-POLICY |

| AVT Profile | AVT ID |
| ----------- | ------ |
| TELEPRES-AVT-POLICY-DEFAULT | 1 |
| TELEPRES-AVT-POLICY-VOICE | 2 |

##### VRF customer114

| AVT policy |
| ---------- |
| TELEPRES-AVT-POLICY |

| AVT Profile | AVT ID |
| ----------- | ------ |
| TELEPRES-AVT-POLICY-DEFAULT | 1 |
| TELEPRES-AVT-POLICY-VOICE | 2 |

##### VRF customer115

| AVT policy |
| ---------- |
| TELEPRES-AVT-POLICY |

| AVT Profile | AVT ID |
| ----------- | ------ |
| TELEPRES-AVT-POLICY-DEFAULT | 1 |
| TELEPRES-AVT-POLICY-VOICE | 2 |

##### VRF customer116

| AVT policy |
| ---------- |
| TELEPRES-AVT-POLICY |

| AVT Profile | AVT ID |
| ----------- | ------ |
| TELEPRES-AVT-POLICY-DEFAULT | 1 |
| TELEPRES-AVT-POLICY-VOICE | 2 |

##### VRF customer117

| AVT policy |
| ---------- |
| TELEPRES-AVT-POLICY |

| AVT Profile | AVT ID |
| ----------- | ------ |
| TELEPRES-AVT-POLICY-DEFAULT | 1 |
| TELEPRES-AVT-POLICY-VOICE | 2 |

##### VRF customer118

| AVT policy |
| ---------- |
| TELEPRES-AVT-POLICY |

| AVT Profile | AVT ID |
| ----------- | ------ |
| TELEPRES-AVT-POLICY-DEFAULT | 1 |
| TELEPRES-AVT-POLICY-VOICE | 2 |

##### VRF customer119

| AVT policy |
| ---------- |
| TELEPRES-AVT-POLICY |

| AVT Profile | AVT ID |
| ----------- | ------ |
| TELEPRES-AVT-POLICY-DEFAULT | 1 |
| TELEPRES-AVT-POLICY-VOICE | 2 |

##### VRF customer120

| AVT policy |
| ---------- |
| TELEPRES-AVT-POLICY |

| AVT Profile | AVT ID |
| ----------- | ------ |
| TELEPRES-AVT-POLICY-DEFAULT | 1 |
| TELEPRES-AVT-POLICY-VOICE | 2 |

##### VRF customer121

| AVT policy |
| ---------- |
| TELEPRES-AVT-POLICY |

| AVT Profile | AVT ID |
| ----------- | ------ |
| TELEPRES-AVT-POLICY-DEFAULT | 1 |
| TELEPRES-AVT-POLICY-VOICE | 2 |

##### VRF customer122

| AVT policy |
| ---------- |
| TELEPRES-AVT-POLICY |

| AVT Profile | AVT ID |
| ----------- | ------ |
| TELEPRES-AVT-POLICY-DEFAULT | 1 |
| TELEPRES-AVT-POLICY-VOICE | 2 |

##### VRF customer123

| AVT policy |
| ---------- |
| TELEPRES-AVT-POLICY |

| AVT Profile | AVT ID |
| ----------- | ------ |
| TELEPRES-AVT-POLICY-DEFAULT | 1 |
| TELEPRES-AVT-POLICY-VOICE | 2 |

##### VRF customer124

| AVT policy |
| ---------- |
| TELEPRES-AVT-POLICY |

| AVT Profile | AVT ID |
| ----------- | ------ |
| TELEPRES-AVT-POLICY-DEFAULT | 1 |
| TELEPRES-AVT-POLICY-VOICE | 2 |

##### VRF customer125

| AVT policy |
| ---------- |
| TELEPRES-AVT-POLICY |

| AVT Profile | AVT ID |
| ----------- | ------ |
| TELEPRES-AVT-POLICY-DEFAULT | 1 |
| TELEPRES-AVT-POLICY-VOICE | 2 |

##### VRF customer126

| AVT policy |
| ---------- |
| TELEPRES-AVT-POLICY |

| AVT Profile | AVT ID |
| ----------- | ------ |
| TELEPRES-AVT-POLICY-DEFAULT | 1 |
| TELEPRES-AVT-POLICY-VOICE | 2 |

##### VRF customer127

| AVT policy |
| ---------- |
| TELEPRES-AVT-POLICY |

| AVT Profile | AVT ID |
| ----------- | ------ |
| TELEPRES-AVT-POLICY-DEFAULT | 1 |
| TELEPRES-AVT-POLICY-VOICE | 2 |

##### VRF customer128

| AVT policy |
| ---------- |
| TELEPRES-AVT-POLICY |

| AVT Profile | AVT ID |
| ----------- | ------ |
| TELEPRES-AVT-POLICY-DEFAULT | 1 |
| TELEPRES-AVT-POLICY-VOICE | 2 |

##### VRF customer129

| AVT policy |
| ---------- |
| TELEPRES-AVT-POLICY |

| AVT Profile | AVT ID |
| ----------- | ------ |
| TELEPRES-AVT-POLICY-DEFAULT | 1 |
| TELEPRES-AVT-POLICY-VOICE | 2 |

##### VRF customer130

| AVT policy |
| ---------- |
| TELEPRES-AVT-POLICY |

| AVT Profile | AVT ID |
| ----------- | ------ |
| TELEPRES-AVT-POLICY-DEFAULT | 1 |
| TELEPRES-AVT-POLICY-VOICE | 2 |

##### VRF customer131

| AVT policy |
| ---------- |
| TELEPRES-AVT-POLICY |

| AVT Profile | AVT ID |
| ----------- | ------ |
| TELEPRES-AVT-POLICY-DEFAULT | 1 |
| TELEPRES-AVT-POLICY-VOICE | 2 |

##### VRF customer132

| AVT policy |
| ---------- |
| TELEPRES-AVT-POLICY |

| AVT Profile | AVT ID |
| ----------- | ------ |
| TELEPRES-AVT-POLICY-DEFAULT | 1 |
| TELEPRES-AVT-POLICY-VOICE | 2 |

##### VRF customer133

| AVT policy |
| ---------- |
| TELEPRES-AVT-POLICY |

| AVT Profile | AVT ID |
| ----------- | ------ |
| TELEPRES-AVT-POLICY-DEFAULT | 1 |
| TELEPRES-AVT-POLICY-VOICE | 2 |

##### VRF customer134

| AVT policy |
| ---------- |
| TELEPRES-AVT-POLICY |

| AVT Profile | AVT ID |
| ----------- | ------ |
| TELEPRES-AVT-POLICY-DEFAULT | 1 |
| TELEPRES-AVT-POLICY-VOICE | 2 |

##### VRF customer135

| AVT policy |
| ---------- |
| TELEPRES-AVT-POLICY |

| AVT Profile | AVT ID |
| ----------- | ------ |
| TELEPRES-AVT-POLICY-DEFAULT | 1 |
| TELEPRES-AVT-POLICY-VOICE | 2 |

##### VRF customer136

| AVT policy |
| ---------- |
| TELEPRES-AVT-POLICY |

| AVT Profile | AVT ID |
| ----------- | ------ |
| TELEPRES-AVT-POLICY-DEFAULT | 1 |
| TELEPRES-AVT-POLICY-VOICE | 2 |

##### VRF customer137

| AVT policy |
| ---------- |
| TELEPRES-AVT-POLICY |

| AVT Profile | AVT ID |
| ----------- | ------ |
| TELEPRES-AVT-POLICY-DEFAULT | 1 |
| TELEPRES-AVT-POLICY-VOICE | 2 |

##### VRF customer138

| AVT policy |
| ---------- |
| TELEPRES-AVT-POLICY |

| AVT Profile | AVT ID |
| ----------- | ------ |
| TELEPRES-AVT-POLICY-DEFAULT | 1 |
| TELEPRES-AVT-POLICY-VOICE | 2 |

##### VRF customer139

| AVT policy |
| ---------- |
| TELEPRES-AVT-POLICY |

| AVT Profile | AVT ID |
| ----------- | ------ |
| TELEPRES-AVT-POLICY-DEFAULT | 1 |
| TELEPRES-AVT-POLICY-VOICE | 2 |

##### VRF customer140

| AVT policy |
| ---------- |
| TELEPRES-AVT-POLICY |

| AVT Profile | AVT ID |
| ----------- | ------ |
| TELEPRES-AVT-POLICY-DEFAULT | 1 |
| TELEPRES-AVT-POLICY-VOICE | 2 |

##### VRF customer141

| AVT policy |
| ---------- |
| TELEPRES-AVT-POLICY |

| AVT Profile | AVT ID |
| ----------- | ------ |
| TELEPRES-AVT-POLICY-DEFAULT | 1 |
| TELEPRES-AVT-POLICY-VOICE | 2 |

##### VRF customer142

| AVT policy |
| ---------- |
| TELEPRES-AVT-POLICY |

| AVT Profile | AVT ID |
| ----------- | ------ |
| TELEPRES-AVT-POLICY-DEFAULT | 1 |
| TELEPRES-AVT-POLICY-VOICE | 2 |

##### VRF customer143

| AVT policy |
| ---------- |
| TELEPRES-AVT-POLICY |

| AVT Profile | AVT ID |
| ----------- | ------ |
| TELEPRES-AVT-POLICY-DEFAULT | 1 |
| TELEPRES-AVT-POLICY-VOICE | 2 |

##### VRF customer144

| AVT policy |
| ---------- |
| TELEPRES-AVT-POLICY |

| AVT Profile | AVT ID |
| ----------- | ------ |
| TELEPRES-AVT-POLICY-DEFAULT | 1 |
| TELEPRES-AVT-POLICY-VOICE | 2 |

##### VRF customer145

| AVT policy |
| ---------- |
| TELEPRES-AVT-POLICY |

| AVT Profile | AVT ID |
| ----------- | ------ |
| TELEPRES-AVT-POLICY-DEFAULT | 1 |
| TELEPRES-AVT-POLICY-VOICE | 2 |

##### VRF customer146

| AVT policy |
| ---------- |
| TELEPRES-AVT-POLICY |

| AVT Profile | AVT ID |
| ----------- | ------ |
| TELEPRES-AVT-POLICY-DEFAULT | 1 |
| TELEPRES-AVT-POLICY-VOICE | 2 |

##### VRF customer147

| AVT policy |
| ---------- |
| TELEPRES-AVT-POLICY |

| AVT Profile | AVT ID |
| ----------- | ------ |
| TELEPRES-AVT-POLICY-DEFAULT | 1 |
| TELEPRES-AVT-POLICY-VOICE | 2 |

##### VRF customer148

| AVT policy |
| ---------- |
| TELEPRES-AVT-POLICY |

| AVT Profile | AVT ID |
| ----------- | ------ |
| TELEPRES-AVT-POLICY-DEFAULT | 1 |
| TELEPRES-AVT-POLICY-VOICE | 2 |

##### VRF customer149

| AVT policy |
| ---------- |
| TELEPRES-AVT-POLICY |

| AVT Profile | AVT ID |
| ----------- | ------ |
| TELEPRES-AVT-POLICY-DEFAULT | 1 |
| TELEPRES-AVT-POLICY-VOICE | 2 |

##### VRF customer150

| AVT policy |
| ---------- |
| TELEPRES-AVT-POLICY |

| AVT Profile | AVT ID |
| ----------- | ------ |
| TELEPRES-AVT-POLICY-DEFAULT | 1 |
| TELEPRES-AVT-POLICY-VOICE | 2 |

##### VRF customer151

| AVT policy |
| ---------- |
| TELEPRES-AVT-POLICY |

| AVT Profile | AVT ID |
| ----------- | ------ |
| TELEPRES-AVT-POLICY-DEFAULT | 1 |
| TELEPRES-AVT-POLICY-VOICE | 2 |

##### VRF customer152

| AVT policy |
| ---------- |
| TELEPRES-AVT-POLICY |

| AVT Profile | AVT ID |
| ----------- | ------ |
| TELEPRES-AVT-POLICY-DEFAULT | 1 |
| TELEPRES-AVT-POLICY-VOICE | 2 |

##### VRF customer153

| AVT policy |
| ---------- |
| TELEPRES-AVT-POLICY |

| AVT Profile | AVT ID |
| ----------- | ------ |
| TELEPRES-AVT-POLICY-DEFAULT | 1 |
| TELEPRES-AVT-POLICY-VOICE | 2 |

##### VRF customer154

| AVT policy |
| ---------- |
| TELEPRES-AVT-POLICY |

| AVT Profile | AVT ID |
| ----------- | ------ |
| TELEPRES-AVT-POLICY-DEFAULT | 1 |
| TELEPRES-AVT-POLICY-VOICE | 2 |

##### VRF customer155

| AVT policy |
| ---------- |
| TELEPRES-AVT-POLICY |

| AVT Profile | AVT ID |
| ----------- | ------ |
| TELEPRES-AVT-POLICY-DEFAULT | 1 |
| TELEPRES-AVT-POLICY-VOICE | 2 |

##### VRF customer156

| AVT policy |
| ---------- |
| TELEPRES-AVT-POLICY |

| AVT Profile | AVT ID |
| ----------- | ------ |
| TELEPRES-AVT-POLICY-DEFAULT | 1 |
| TELEPRES-AVT-POLICY-VOICE | 2 |

##### VRF customer157

| AVT policy |
| ---------- |
| TELEPRES-AVT-POLICY |

| AVT Profile | AVT ID |
| ----------- | ------ |
| TELEPRES-AVT-POLICY-DEFAULT | 1 |
| TELEPRES-AVT-POLICY-VOICE | 2 |

##### VRF customer158

| AVT policy |
| ---------- |
| TELEPRES-AVT-POLICY |

| AVT Profile | AVT ID |
| ----------- | ------ |
| TELEPRES-AVT-POLICY-DEFAULT | 1 |
| TELEPRES-AVT-POLICY-VOICE | 2 |

##### VRF customer159

| AVT policy |
| ---------- |
| TELEPRES-AVT-POLICY |

| AVT Profile | AVT ID |
| ----------- | ------ |
| TELEPRES-AVT-POLICY-DEFAULT | 1 |
| TELEPRES-AVT-POLICY-VOICE | 2 |

##### VRF customer160

| AVT policy |
| ---------- |
| TELEPRES-AVT-POLICY |

| AVT Profile | AVT ID |
| ----------- | ------ |
| TELEPRES-AVT-POLICY-DEFAULT | 1 |
| TELEPRES-AVT-POLICY-VOICE | 2 |

##### VRF customer161

| AVT policy |
| ---------- |
| TELEPRES-AVT-POLICY |

| AVT Profile | AVT ID |
| ----------- | ------ |
| TELEPRES-AVT-POLICY-DEFAULT | 1 |
| TELEPRES-AVT-POLICY-VOICE | 2 |

##### VRF customer162

| AVT policy |
| ---------- |
| TELEPRES-AVT-POLICY |

| AVT Profile | AVT ID |
| ----------- | ------ |
| TELEPRES-AVT-POLICY-DEFAULT | 1 |
| TELEPRES-AVT-POLICY-VOICE | 2 |

##### VRF customer163

| AVT policy |
| ---------- |
| TELEPRES-AVT-POLICY |

| AVT Profile | AVT ID |
| ----------- | ------ |
| TELEPRES-AVT-POLICY-DEFAULT | 1 |
| TELEPRES-AVT-POLICY-VOICE | 2 |

##### VRF customer164

| AVT policy |
| ---------- |
| TELEPRES-AVT-POLICY |

| AVT Profile | AVT ID |
| ----------- | ------ |
| TELEPRES-AVT-POLICY-DEFAULT | 1 |
| TELEPRES-AVT-POLICY-VOICE | 2 |

##### VRF customer165

| AVT policy |
| ---------- |
| TELEPRES-AVT-POLICY |

| AVT Profile | AVT ID |
| ----------- | ------ |
| TELEPRES-AVT-POLICY-DEFAULT | 1 |
| TELEPRES-AVT-POLICY-VOICE | 2 |

##### VRF customer166

| AVT policy |
| ---------- |
| TELEPRES-AVT-POLICY |

| AVT Profile | AVT ID |
| ----------- | ------ |
| TELEPRES-AVT-POLICY-DEFAULT | 1 |
| TELEPRES-AVT-POLICY-VOICE | 2 |

##### VRF customer167

| AVT policy |
| ---------- |
| TELEPRES-AVT-POLICY |

| AVT Profile | AVT ID |
| ----------- | ------ |
| TELEPRES-AVT-POLICY-DEFAULT | 1 |
| TELEPRES-AVT-POLICY-VOICE | 2 |

##### VRF customer168

| AVT policy |
| ---------- |
| TELEPRES-AVT-POLICY |

| AVT Profile | AVT ID |
| ----------- | ------ |
| TELEPRES-AVT-POLICY-DEFAULT | 1 |
| TELEPRES-AVT-POLICY-VOICE | 2 |

##### VRF customer169

| AVT policy |
| ---------- |
| TELEPRES-AVT-POLICY |

| AVT Profile | AVT ID |
| ----------- | ------ |
| TELEPRES-AVT-POLICY-DEFAULT | 1 |
| TELEPRES-AVT-POLICY-VOICE | 2 |

##### VRF customer170

| AVT policy |
| ---------- |
| TELEPRES-AVT-POLICY |

| AVT Profile | AVT ID |
| ----------- | ------ |
| TELEPRES-AVT-POLICY-DEFAULT | 1 |
| TELEPRES-AVT-POLICY-VOICE | 2 |

##### VRF customer171

| AVT policy |
| ---------- |
| TELEPRES-AVT-POLICY |

| AVT Profile | AVT ID |
| ----------- | ------ |
| TELEPRES-AVT-POLICY-DEFAULT | 1 |
| TELEPRES-AVT-POLICY-VOICE | 2 |

##### VRF customer172

| AVT policy |
| ---------- |
| TELEPRES-AVT-POLICY |

| AVT Profile | AVT ID |
| ----------- | ------ |
| TELEPRES-AVT-POLICY-DEFAULT | 1 |
| TELEPRES-AVT-POLICY-VOICE | 2 |

##### VRF customer173

| AVT policy |
| ---------- |
| TELEPRES-AVT-POLICY |

| AVT Profile | AVT ID |
| ----------- | ------ |
| TELEPRES-AVT-POLICY-DEFAULT | 1 |
| TELEPRES-AVT-POLICY-VOICE | 2 |

##### VRF customer174

| AVT policy |
| ---------- |
| TELEPRES-AVT-POLICY |

| AVT Profile | AVT ID |
| ----------- | ------ |
| TELEPRES-AVT-POLICY-DEFAULT | 1 |
| TELEPRES-AVT-POLICY-VOICE | 2 |

##### VRF customer175

| AVT policy |
| ---------- |
| TELEPRES-AVT-POLICY |

| AVT Profile | AVT ID |
| ----------- | ------ |
| TELEPRES-AVT-POLICY-DEFAULT | 1 |
| TELEPRES-AVT-POLICY-VOICE | 2 |

##### VRF customer176

| AVT policy |
| ---------- |
| TELEPRES-AVT-POLICY |

| AVT Profile | AVT ID |
| ----------- | ------ |
| TELEPRES-AVT-POLICY-DEFAULT | 1 |
| TELEPRES-AVT-POLICY-VOICE | 2 |

##### VRF customer177

| AVT policy |
| ---------- |
| TELEPRES-AVT-POLICY |

| AVT Profile | AVT ID |
| ----------- | ------ |
| TELEPRES-AVT-POLICY-DEFAULT | 1 |
| TELEPRES-AVT-POLICY-VOICE | 2 |

##### VRF customer178

| AVT policy |
| ---------- |
| TELEPRES-AVT-POLICY |

| AVT Profile | AVT ID |
| ----------- | ------ |
| TELEPRES-AVT-POLICY-DEFAULT | 1 |
| TELEPRES-AVT-POLICY-VOICE | 2 |

##### VRF customer179

| AVT policy |
| ---------- |
| TELEPRES-AVT-POLICY |

| AVT Profile | AVT ID |
| ----------- | ------ |
| TELEPRES-AVT-POLICY-DEFAULT | 1 |
| TELEPRES-AVT-POLICY-VOICE | 2 |

##### VRF customer180

| AVT policy |
| ---------- |
| TELEPRES-AVT-POLICY |

| AVT Profile | AVT ID |
| ----------- | ------ |
| TELEPRES-AVT-POLICY-DEFAULT | 1 |
| TELEPRES-AVT-POLICY-VOICE | 2 |

##### VRF customer181

| AVT policy |
| ---------- |
| TELEPRES-AVT-POLICY |

| AVT Profile | AVT ID |
| ----------- | ------ |
| TELEPRES-AVT-POLICY-DEFAULT | 1 |
| TELEPRES-AVT-POLICY-VOICE | 2 |

##### VRF customer182

| AVT policy |
| ---------- |
| TELEPRES-AVT-POLICY |

| AVT Profile | AVT ID |
| ----------- | ------ |
| TELEPRES-AVT-POLICY-DEFAULT | 1 |
| TELEPRES-AVT-POLICY-VOICE | 2 |

##### VRF customer183

| AVT policy |
| ---------- |
| TELEPRES-AVT-POLICY |

| AVT Profile | AVT ID |
| ----------- | ------ |
| TELEPRES-AVT-POLICY-DEFAULT | 1 |
| TELEPRES-AVT-POLICY-VOICE | 2 |

##### VRF customer184

| AVT policy |
| ---------- |
| TELEPRES-AVT-POLICY |

| AVT Profile | AVT ID |
| ----------- | ------ |
| TELEPRES-AVT-POLICY-DEFAULT | 1 |
| TELEPRES-AVT-POLICY-VOICE | 2 |

##### VRF customer185

| AVT policy |
| ---------- |
| TELEPRES-AVT-POLICY |

| AVT Profile | AVT ID |
| ----------- | ------ |
| TELEPRES-AVT-POLICY-DEFAULT | 1 |
| TELEPRES-AVT-POLICY-VOICE | 2 |

##### VRF customer186

| AVT policy |
| ---------- |
| TELEPRES-AVT-POLICY |

| AVT Profile | AVT ID |
| ----------- | ------ |
| TELEPRES-AVT-POLICY-DEFAULT | 1 |
| TELEPRES-AVT-POLICY-VOICE | 2 |

##### VRF customer187

| AVT policy |
| ---------- |
| TELEPRES-AVT-POLICY |

| AVT Profile | AVT ID |
| ----------- | ------ |
| TELEPRES-AVT-POLICY-DEFAULT | 1 |
| TELEPRES-AVT-POLICY-VOICE | 2 |

##### VRF customer188

| AVT policy |
| ---------- |
| TELEPRES-AVT-POLICY |

| AVT Profile | AVT ID |
| ----------- | ------ |
| TELEPRES-AVT-POLICY-DEFAULT | 1 |
| TELEPRES-AVT-POLICY-VOICE | 2 |

##### VRF customer189

| AVT policy |
| ---------- |
| TELEPRES-AVT-POLICY |

| AVT Profile | AVT ID |
| ----------- | ------ |
| TELEPRES-AVT-POLICY-DEFAULT | 1 |
| TELEPRES-AVT-POLICY-VOICE | 2 |

##### VRF customer190

| AVT policy |
| ---------- |
| TELEPRES-AVT-POLICY |

| AVT Profile | AVT ID |
| ----------- | ------ |
| TELEPRES-AVT-POLICY-DEFAULT | 1 |
| TELEPRES-AVT-POLICY-VOICE | 2 |

##### VRF customer191

| AVT policy |
| ---------- |
| TELEPRES-AVT-POLICY |

| AVT Profile | AVT ID |
| ----------- | ------ |
| TELEPRES-AVT-POLICY-DEFAULT | 1 |
| TELEPRES-AVT-POLICY-VOICE | 2 |

##### VRF customer192

| AVT policy |
| ---------- |
| TELEPRES-AVT-POLICY |

| AVT Profile | AVT ID |
| ----------- | ------ |
| TELEPRES-AVT-POLICY-DEFAULT | 1 |
| TELEPRES-AVT-POLICY-VOICE | 2 |

##### VRF customer193

| AVT policy |
| ---------- |
| TELEPRES-AVT-POLICY |

| AVT Profile | AVT ID |
| ----------- | ------ |
| TELEPRES-AVT-POLICY-DEFAULT | 1 |
| TELEPRES-AVT-POLICY-VOICE | 2 |

##### VRF customer194

| AVT policy |
| ---------- |
| TELEPRES-AVT-POLICY |

| AVT Profile | AVT ID |
| ----------- | ------ |
| TELEPRES-AVT-POLICY-DEFAULT | 1 |
| TELEPRES-AVT-POLICY-VOICE | 2 |

##### VRF customer195

| AVT policy |
| ---------- |
| TELEPRES-AVT-POLICY |

| AVT Profile | AVT ID |
| ----------- | ------ |
| TELEPRES-AVT-POLICY-DEFAULT | 1 |
| TELEPRES-AVT-POLICY-VOICE | 2 |

##### VRF customer196

| AVT policy |
| ---------- |
| TELEPRES-AVT-POLICY |

| AVT Profile | AVT ID |
| ----------- | ------ |
| TELEPRES-AVT-POLICY-DEFAULT | 1 |
| TELEPRES-AVT-POLICY-VOICE | 2 |

##### VRF customer197

| AVT policy |
| ---------- |
| TELEPRES-AVT-POLICY |

| AVT Profile | AVT ID |
| ----------- | ------ |
| TELEPRES-AVT-POLICY-DEFAULT | 1 |
| TELEPRES-AVT-POLICY-VOICE | 2 |

##### VRF customer198

| AVT policy |
| ---------- |
| TELEPRES-AVT-POLICY |

| AVT Profile | AVT ID |
| ----------- | ------ |
| TELEPRES-AVT-POLICY-DEFAULT | 1 |
| TELEPRES-AVT-POLICY-VOICE | 2 |

##### VRF customer199

| AVT policy |
| ---------- |
| TELEPRES-AVT-POLICY |

| AVT Profile | AVT ID |
| ----------- | ------ |
| TELEPRES-AVT-POLICY-DEFAULT | 1 |
| TELEPRES-AVT-POLICY-VOICE | 2 |

##### VRF customer200

| AVT policy |
| ---------- |
| TELEPRES-AVT-POLICY |

| AVT Profile | AVT ID |
| ----------- | ------ |
| TELEPRES-AVT-POLICY-DEFAULT | 1 |
| TELEPRES-AVT-POLICY-VOICE | 2 |

##### VRF customer201

| AVT policy |
| ---------- |
| TELEPRES-AVT-POLICY |

| AVT Profile | AVT ID |
| ----------- | ------ |
| TELEPRES-AVT-POLICY-DEFAULT | 1 |
| TELEPRES-AVT-POLICY-VOICE | 2 |

##### VRF customer202

| AVT policy |
| ---------- |
| TELEPRES-AVT-POLICY |

| AVT Profile | AVT ID |
| ----------- | ------ |
| TELEPRES-AVT-POLICY-DEFAULT | 1 |
| TELEPRES-AVT-POLICY-VOICE | 2 |

##### VRF customer203

| AVT policy |
| ---------- |
| TELEPRES-AVT-POLICY |

| AVT Profile | AVT ID |
| ----------- | ------ |
| TELEPRES-AVT-POLICY-DEFAULT | 1 |
| TELEPRES-AVT-POLICY-VOICE | 2 |

##### VRF customer204

| AVT policy |
| ---------- |
| TELEPRES-AVT-POLICY |

| AVT Profile | AVT ID |
| ----------- | ------ |
| TELEPRES-AVT-POLICY-DEFAULT | 1 |
| TELEPRES-AVT-POLICY-VOICE | 2 |

##### VRF customer205

| AVT policy |
| ---------- |
| TELEPRES-AVT-POLICY |

| AVT Profile | AVT ID |
| ----------- | ------ |
| TELEPRES-AVT-POLICY-DEFAULT | 1 |
| TELEPRES-AVT-POLICY-VOICE | 2 |

##### VRF customer206

| AVT policy |
| ---------- |
| TELEPRES-AVT-POLICY |

| AVT Profile | AVT ID |
| ----------- | ------ |
| TELEPRES-AVT-POLICY-DEFAULT | 1 |
| TELEPRES-AVT-POLICY-VOICE | 2 |

##### VRF customer207

| AVT policy |
| ---------- |
| TELEPRES-AVT-POLICY |

| AVT Profile | AVT ID |
| ----------- | ------ |
| TELEPRES-AVT-POLICY-DEFAULT | 1 |
| TELEPRES-AVT-POLICY-VOICE | 2 |

##### VRF customer208

| AVT policy |
| ---------- |
| TELEPRES-AVT-POLICY |

| AVT Profile | AVT ID |
| ----------- | ------ |
| TELEPRES-AVT-POLICY-DEFAULT | 1 |
| TELEPRES-AVT-POLICY-VOICE | 2 |

##### VRF customer209

| AVT policy |
| ---------- |
| TELEPRES-AVT-POLICY |

| AVT Profile | AVT ID |
| ----------- | ------ |
| TELEPRES-AVT-POLICY-DEFAULT | 1 |
| TELEPRES-AVT-POLICY-VOICE | 2 |

##### VRF customer210

| AVT policy |
| ---------- |
| TELEPRES-AVT-POLICY |

| AVT Profile | AVT ID |
| ----------- | ------ |
| TELEPRES-AVT-POLICY-DEFAULT | 1 |
| TELEPRES-AVT-POLICY-VOICE | 2 |

##### VRF customer211

| AVT policy |
| ---------- |
| TELEPRES-AVT-POLICY |

| AVT Profile | AVT ID |
| ----------- | ------ |
| TELEPRES-AVT-POLICY-DEFAULT | 1 |
| TELEPRES-AVT-POLICY-VOICE | 2 |

##### VRF customer212

| AVT policy |
| ---------- |
| TELEPRES-AVT-POLICY |

| AVT Profile | AVT ID |
| ----------- | ------ |
| TELEPRES-AVT-POLICY-DEFAULT | 1 |
| TELEPRES-AVT-POLICY-VOICE | 2 |

##### VRF customer213

| AVT policy |
| ---------- |
| TELEPRES-AVT-POLICY |

| AVT Profile | AVT ID |
| ----------- | ------ |
| TELEPRES-AVT-POLICY-DEFAULT | 1 |
| TELEPRES-AVT-POLICY-VOICE | 2 |

##### VRF customer214

| AVT policy |
| ---------- |
| TELEPRES-AVT-POLICY |

| AVT Profile | AVT ID |
| ----------- | ------ |
| TELEPRES-AVT-POLICY-DEFAULT | 1 |
| TELEPRES-AVT-POLICY-VOICE | 2 |

##### VRF customer215

| AVT policy |
| ---------- |
| TELEPRES-AVT-POLICY |

| AVT Profile | AVT ID |
| ----------- | ------ |
| TELEPRES-AVT-POLICY-DEFAULT | 1 |
| TELEPRES-AVT-POLICY-VOICE | 2 |

##### VRF customer216

| AVT policy |
| ---------- |
| TELEPRES-AVT-POLICY |

| AVT Profile | AVT ID |
| ----------- | ------ |
| TELEPRES-AVT-POLICY-DEFAULT | 1 |
| TELEPRES-AVT-POLICY-VOICE | 2 |

##### VRF customer217

| AVT policy |
| ---------- |
| TELEPRES-AVT-POLICY |

| AVT Profile | AVT ID |
| ----------- | ------ |
| TELEPRES-AVT-POLICY-DEFAULT | 1 |
| TELEPRES-AVT-POLICY-VOICE | 2 |

##### VRF customer218

| AVT policy |
| ---------- |
| TELEPRES-AVT-POLICY |

| AVT Profile | AVT ID |
| ----------- | ------ |
| TELEPRES-AVT-POLICY-DEFAULT | 1 |
| TELEPRES-AVT-POLICY-VOICE | 2 |

##### VRF customer219

| AVT policy |
| ---------- |
| TELEPRES-AVT-POLICY |

| AVT Profile | AVT ID |
| ----------- | ------ |
| TELEPRES-AVT-POLICY-DEFAULT | 1 |
| TELEPRES-AVT-POLICY-VOICE | 2 |

##### VRF customer220

| AVT policy |
| ---------- |
| TELEPRES-AVT-POLICY |

| AVT Profile | AVT ID |
| ----------- | ------ |
| TELEPRES-AVT-POLICY-DEFAULT | 1 |
| TELEPRES-AVT-POLICY-VOICE | 2 |

##### VRF customer221

| AVT policy |
| ---------- |
| TELEPRES-AVT-POLICY |

| AVT Profile | AVT ID |
| ----------- | ------ |
| TELEPRES-AVT-POLICY-DEFAULT | 1 |
| TELEPRES-AVT-POLICY-VOICE | 2 |

##### VRF customer222

| AVT policy |
| ---------- |
| TELEPRES-AVT-POLICY |

| AVT Profile | AVT ID |
| ----------- | ------ |
| TELEPRES-AVT-POLICY-DEFAULT | 1 |
| TELEPRES-AVT-POLICY-VOICE | 2 |

##### VRF customer223

| AVT policy |
| ---------- |
| TELEPRES-AVT-POLICY |

| AVT Profile | AVT ID |
| ----------- | ------ |
| TELEPRES-AVT-POLICY-DEFAULT | 1 |
| TELEPRES-AVT-POLICY-VOICE | 2 |

##### VRF customer224

| AVT policy |
| ---------- |
| TELEPRES-AVT-POLICY |

| AVT Profile | AVT ID |
| ----------- | ------ |
| TELEPRES-AVT-POLICY-DEFAULT | 1 |
| TELEPRES-AVT-POLICY-VOICE | 2 |

##### VRF customer225

| AVT policy |
| ---------- |
| TELEPRES-AVT-POLICY |

| AVT Profile | AVT ID |
| ----------- | ------ |
| TELEPRES-AVT-POLICY-DEFAULT | 1 |
| TELEPRES-AVT-POLICY-VOICE | 2 |

##### VRF customer226

| AVT policy |
| ---------- |
| TELEPRES-AVT-POLICY |

| AVT Profile | AVT ID |
| ----------- | ------ |
| TELEPRES-AVT-POLICY-DEFAULT | 1 |
| TELEPRES-AVT-POLICY-VOICE | 2 |

##### VRF customer227

| AVT policy |
| ---------- |
| TELEPRES-AVT-POLICY |

| AVT Profile | AVT ID |
| ----------- | ------ |
| TELEPRES-AVT-POLICY-DEFAULT | 1 |
| TELEPRES-AVT-POLICY-VOICE | 2 |

##### VRF customer228

| AVT policy |
| ---------- |
| TELEPRES-AVT-POLICY |

| AVT Profile | AVT ID |
| ----------- | ------ |
| TELEPRES-AVT-POLICY-DEFAULT | 1 |
| TELEPRES-AVT-POLICY-VOICE | 2 |

##### VRF customer229

| AVT policy |
| ---------- |
| TELEPRES-AVT-POLICY |

| AVT Profile | AVT ID |
| ----------- | ------ |
| TELEPRES-AVT-POLICY-DEFAULT | 1 |
| TELEPRES-AVT-POLICY-VOICE | 2 |

##### VRF customer230

| AVT policy |
| ---------- |
| TELEPRES-AVT-POLICY |

| AVT Profile | AVT ID |
| ----------- | ------ |
| TELEPRES-AVT-POLICY-DEFAULT | 1 |
| TELEPRES-AVT-POLICY-VOICE | 2 |

##### VRF customer231

| AVT policy |
| ---------- |
| TELEPRES-AVT-POLICY |

| AVT Profile | AVT ID |
| ----------- | ------ |
| TELEPRES-AVT-POLICY-DEFAULT | 1 |
| TELEPRES-AVT-POLICY-VOICE | 2 |

##### VRF customer232

| AVT policy |
| ---------- |
| TELEPRES-AVT-POLICY |

| AVT Profile | AVT ID |
| ----------- | ------ |
| TELEPRES-AVT-POLICY-DEFAULT | 1 |
| TELEPRES-AVT-POLICY-VOICE | 2 |

##### VRF customer233

| AVT policy |
| ---------- |
| TELEPRES-AVT-POLICY |

| AVT Profile | AVT ID |
| ----------- | ------ |
| TELEPRES-AVT-POLICY-DEFAULT | 1 |
| TELEPRES-AVT-POLICY-VOICE | 2 |

##### VRF customer234

| AVT policy |
| ---------- |
| TELEPRES-AVT-POLICY |

| AVT Profile | AVT ID |
| ----------- | ------ |
| TELEPRES-AVT-POLICY-DEFAULT | 1 |
| TELEPRES-AVT-POLICY-VOICE | 2 |

##### VRF customer235

| AVT policy |
| ---------- |
| TELEPRES-AVT-POLICY |

| AVT Profile | AVT ID |
| ----------- | ------ |
| TELEPRES-AVT-POLICY-DEFAULT | 1 |
| TELEPRES-AVT-POLICY-VOICE | 2 |

##### VRF customer236

| AVT policy |
| ---------- |
| TELEPRES-AVT-POLICY |

| AVT Profile | AVT ID |
| ----------- | ------ |
| TELEPRES-AVT-POLICY-DEFAULT | 1 |
| TELEPRES-AVT-POLICY-VOICE | 2 |

##### VRF customer237

| AVT policy |
| ---------- |
| TELEPRES-AVT-POLICY |

| AVT Profile | AVT ID |
| ----------- | ------ |
| TELEPRES-AVT-POLICY-DEFAULT | 1 |
| TELEPRES-AVT-POLICY-VOICE | 2 |

##### VRF customer238

| AVT policy |
| ---------- |
| TELEPRES-AVT-POLICY |

| AVT Profile | AVT ID |
| ----------- | ------ |
| TELEPRES-AVT-POLICY-DEFAULT | 1 |
| TELEPRES-AVT-POLICY-VOICE | 2 |

##### VRF customer239

| AVT policy |
| ---------- |
| TELEPRES-AVT-POLICY |

| AVT Profile | AVT ID |
| ----------- | ------ |
| TELEPRES-AVT-POLICY-DEFAULT | 1 |
| TELEPRES-AVT-POLICY-VOICE | 2 |

##### VRF customer240

| AVT policy |
| ---------- |
| TELEPRES-AVT-POLICY |

| AVT Profile | AVT ID |
| ----------- | ------ |
| TELEPRES-AVT-POLICY-DEFAULT | 1 |
| TELEPRES-AVT-POLICY-VOICE | 2 |

##### VRF customer241

| AVT policy |
| ---------- |
| TELEPRES-AVT-POLICY |

| AVT Profile | AVT ID |
| ----------- | ------ |
| TELEPRES-AVT-POLICY-DEFAULT | 1 |
| TELEPRES-AVT-POLICY-VOICE | 2 |

##### VRF customer242

| AVT policy |
| ---------- |
| TELEPRES-AVT-POLICY |

| AVT Profile | AVT ID |
| ----------- | ------ |
| TELEPRES-AVT-POLICY-DEFAULT | 1 |
| TELEPRES-AVT-POLICY-VOICE | 2 |

##### VRF customer243

| AVT policy |
| ---------- |
| TELEPRES-AVT-POLICY |

| AVT Profile | AVT ID |
| ----------- | ------ |
| TELEPRES-AVT-POLICY-DEFAULT | 1 |
| TELEPRES-AVT-POLICY-VOICE | 2 |

##### VRF customer244

| AVT policy |
| ---------- |
| TELEPRES-AVT-POLICY |

| AVT Profile | AVT ID |
| ----------- | ------ |
| TELEPRES-AVT-POLICY-DEFAULT | 1 |
| TELEPRES-AVT-POLICY-VOICE | 2 |

##### VRF customer245

| AVT policy |
| ---------- |
| TELEPRES-AVT-POLICY |

| AVT Profile | AVT ID |
| ----------- | ------ |
| TELEPRES-AVT-POLICY-DEFAULT | 1 |
| TELEPRES-AVT-POLICY-VOICE | 2 |

##### VRF customer246

| AVT policy |
| ---------- |
| TELEPRES-AVT-POLICY |

| AVT Profile | AVT ID |
| ----------- | ------ |
| TELEPRES-AVT-POLICY-DEFAULT | 1 |
| TELEPRES-AVT-POLICY-VOICE | 2 |

##### VRF customer247

| AVT policy |
| ---------- |
| TELEPRES-AVT-POLICY |

| AVT Profile | AVT ID |
| ----------- | ------ |
| TELEPRES-AVT-POLICY-DEFAULT | 1 |
| TELEPRES-AVT-POLICY-VOICE | 2 |

##### VRF customer248

| AVT policy |
| ---------- |
| TELEPRES-AVT-POLICY |

| AVT Profile | AVT ID |
| ----------- | ------ |
| TELEPRES-AVT-POLICY-DEFAULT | 1 |
| TELEPRES-AVT-POLICY-VOICE | 2 |

##### VRF customer249

| AVT policy |
| ---------- |
| TELEPRES-AVT-POLICY |

| AVT Profile | AVT ID |
| ----------- | ------ |
| TELEPRES-AVT-POLICY-DEFAULT | 1 |
| TELEPRES-AVT-POLICY-VOICE | 2 |

##### VRF customer250

| AVT policy |
| ---------- |
| TELEPRES-AVT-POLICY |

| AVT Profile | AVT ID |
| ----------- | ------ |
| TELEPRES-AVT-POLICY-DEFAULT | 1 |
| TELEPRES-AVT-POLICY-VOICE | 2 |

##### VRF customer251

| AVT policy |
| ---------- |
| TELEPRES-AVT-POLICY |

| AVT Profile | AVT ID |
| ----------- | ------ |
| TELEPRES-AVT-POLICY-DEFAULT | 1 |
| TELEPRES-AVT-POLICY-VOICE | 2 |

##### VRF customer252

| AVT policy |
| ---------- |
| TELEPRES-AVT-POLICY |

| AVT Profile | AVT ID |
| ----------- | ------ |
| TELEPRES-AVT-POLICY-DEFAULT | 1 |
| TELEPRES-AVT-POLICY-VOICE | 2 |

##### VRF customer253

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
   topology role pathfinder
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
   vrf customer65
      avt policy TELEPRES-AVT-POLICY
      avt profile TELEPRES-AVT-POLICY-DEFAULT id 1
      avt profile TELEPRES-AVT-POLICY-VOICE id 2
   !
   vrf customer66
      avt policy TELEPRES-AVT-POLICY
      avt profile TELEPRES-AVT-POLICY-DEFAULT id 1
      avt profile TELEPRES-AVT-POLICY-VOICE id 2
   !
   vrf customer67
      avt policy TELEPRES-AVT-POLICY
      avt profile TELEPRES-AVT-POLICY-DEFAULT id 1
      avt profile TELEPRES-AVT-POLICY-VOICE id 2
   !
   vrf customer68
      avt policy TELEPRES-AVT-POLICY
      avt profile TELEPRES-AVT-POLICY-DEFAULT id 1
      avt profile TELEPRES-AVT-POLICY-VOICE id 2
   !
   vrf customer69
      avt policy TELEPRES-AVT-POLICY
      avt profile TELEPRES-AVT-POLICY-DEFAULT id 1
      avt profile TELEPRES-AVT-POLICY-VOICE id 2
   !
   vrf customer70
      avt policy TELEPRES-AVT-POLICY
      avt profile TELEPRES-AVT-POLICY-DEFAULT id 1
      avt profile TELEPRES-AVT-POLICY-VOICE id 2
   !
   vrf customer71
      avt policy TELEPRES-AVT-POLICY
      avt profile TELEPRES-AVT-POLICY-DEFAULT id 1
      avt profile TELEPRES-AVT-POLICY-VOICE id 2
   !
   vrf customer72
      avt policy TELEPRES-AVT-POLICY
      avt profile TELEPRES-AVT-POLICY-DEFAULT id 1
      avt profile TELEPRES-AVT-POLICY-VOICE id 2
   !
   vrf customer73
      avt policy TELEPRES-AVT-POLICY
      avt profile TELEPRES-AVT-POLICY-DEFAULT id 1
      avt profile TELEPRES-AVT-POLICY-VOICE id 2
   !
   vrf customer74
      avt policy TELEPRES-AVT-POLICY
      avt profile TELEPRES-AVT-POLICY-DEFAULT id 1
      avt profile TELEPRES-AVT-POLICY-VOICE id 2
   !
   vrf customer75
      avt policy TELEPRES-AVT-POLICY
      avt profile TELEPRES-AVT-POLICY-DEFAULT id 1
      avt profile TELEPRES-AVT-POLICY-VOICE id 2
   !
   vrf customer76
      avt policy TELEPRES-AVT-POLICY
      avt profile TELEPRES-AVT-POLICY-DEFAULT id 1
      avt profile TELEPRES-AVT-POLICY-VOICE id 2
   !
   vrf customer77
      avt policy TELEPRES-AVT-POLICY
      avt profile TELEPRES-AVT-POLICY-DEFAULT id 1
      avt profile TELEPRES-AVT-POLICY-VOICE id 2
   !
   vrf customer78
      avt policy TELEPRES-AVT-POLICY
      avt profile TELEPRES-AVT-POLICY-DEFAULT id 1
      avt profile TELEPRES-AVT-POLICY-VOICE id 2
   !
   vrf customer79
      avt policy TELEPRES-AVT-POLICY
      avt profile TELEPRES-AVT-POLICY-DEFAULT id 1
      avt profile TELEPRES-AVT-POLICY-VOICE id 2
   !
   vrf customer80
      avt policy TELEPRES-AVT-POLICY
      avt profile TELEPRES-AVT-POLICY-DEFAULT id 1
      avt profile TELEPRES-AVT-POLICY-VOICE id 2
   !
   vrf customer81
      avt policy TELEPRES-AVT-POLICY
      avt profile TELEPRES-AVT-POLICY-DEFAULT id 1
      avt profile TELEPRES-AVT-POLICY-VOICE id 2
   !
   vrf customer82
      avt policy TELEPRES-AVT-POLICY
      avt profile TELEPRES-AVT-POLICY-DEFAULT id 1
      avt profile TELEPRES-AVT-POLICY-VOICE id 2
   !
   vrf customer83
      avt policy TELEPRES-AVT-POLICY
      avt profile TELEPRES-AVT-POLICY-DEFAULT id 1
      avt profile TELEPRES-AVT-POLICY-VOICE id 2
   !
   vrf customer84
      avt policy TELEPRES-AVT-POLICY
      avt profile TELEPRES-AVT-POLICY-DEFAULT id 1
      avt profile TELEPRES-AVT-POLICY-VOICE id 2
   !
   vrf customer85
      avt policy TELEPRES-AVT-POLICY
      avt profile TELEPRES-AVT-POLICY-DEFAULT id 1
      avt profile TELEPRES-AVT-POLICY-VOICE id 2
   !
   vrf customer86
      avt policy TELEPRES-AVT-POLICY
      avt profile TELEPRES-AVT-POLICY-DEFAULT id 1
      avt profile TELEPRES-AVT-POLICY-VOICE id 2
   !
   vrf customer87
      avt policy TELEPRES-AVT-POLICY
      avt profile TELEPRES-AVT-POLICY-DEFAULT id 1
      avt profile TELEPRES-AVT-POLICY-VOICE id 2
   !
   vrf customer88
      avt policy TELEPRES-AVT-POLICY
      avt profile TELEPRES-AVT-POLICY-DEFAULT id 1
      avt profile TELEPRES-AVT-POLICY-VOICE id 2
   !
   vrf customer89
      avt policy TELEPRES-AVT-POLICY
      avt profile TELEPRES-AVT-POLICY-DEFAULT id 1
      avt profile TELEPRES-AVT-POLICY-VOICE id 2
   !
   vrf customer90
      avt policy TELEPRES-AVT-POLICY
      avt profile TELEPRES-AVT-POLICY-DEFAULT id 1
      avt profile TELEPRES-AVT-POLICY-VOICE id 2
   !
   vrf customer91
      avt policy TELEPRES-AVT-POLICY
      avt profile TELEPRES-AVT-POLICY-DEFAULT id 1
      avt profile TELEPRES-AVT-POLICY-VOICE id 2
   !
   vrf customer92
      avt policy TELEPRES-AVT-POLICY
      avt profile TELEPRES-AVT-POLICY-DEFAULT id 1
      avt profile TELEPRES-AVT-POLICY-VOICE id 2
   !
   vrf customer93
      avt policy TELEPRES-AVT-POLICY
      avt profile TELEPRES-AVT-POLICY-DEFAULT id 1
      avt profile TELEPRES-AVT-POLICY-VOICE id 2
   !
   vrf customer94
      avt policy TELEPRES-AVT-POLICY
      avt profile TELEPRES-AVT-POLICY-DEFAULT id 1
      avt profile TELEPRES-AVT-POLICY-VOICE id 2
   !
   vrf customer95
      avt policy TELEPRES-AVT-POLICY
      avt profile TELEPRES-AVT-POLICY-DEFAULT id 1
      avt profile TELEPRES-AVT-POLICY-VOICE id 2
   !
   vrf customer96
      avt policy TELEPRES-AVT-POLICY
      avt profile TELEPRES-AVT-POLICY-DEFAULT id 1
      avt profile TELEPRES-AVT-POLICY-VOICE id 2
   !
   vrf customer97
      avt policy TELEPRES-AVT-POLICY
      avt profile TELEPRES-AVT-POLICY-DEFAULT id 1
      avt profile TELEPRES-AVT-POLICY-VOICE id 2
   !
   vrf customer98
      avt policy TELEPRES-AVT-POLICY
      avt profile TELEPRES-AVT-POLICY-DEFAULT id 1
      avt profile TELEPRES-AVT-POLICY-VOICE id 2
   !
   vrf customer99
      avt policy TELEPRES-AVT-POLICY
      avt profile TELEPRES-AVT-POLICY-DEFAULT id 1
      avt profile TELEPRES-AVT-POLICY-VOICE id 2
   !
   vrf customer100
      avt policy TELEPRES-AVT-POLICY
      avt profile TELEPRES-AVT-POLICY-DEFAULT id 1
      avt profile TELEPRES-AVT-POLICY-VOICE id 2
   !
   vrf customer101
      avt policy TELEPRES-AVT-POLICY
      avt profile TELEPRES-AVT-POLICY-DEFAULT id 1
      avt profile TELEPRES-AVT-POLICY-VOICE id 2
   !
   vrf customer102
      avt policy TELEPRES-AVT-POLICY
      avt profile TELEPRES-AVT-POLICY-DEFAULT id 1
      avt profile TELEPRES-AVT-POLICY-VOICE id 2
   !
   vrf customer103
      avt policy TELEPRES-AVT-POLICY
      avt profile TELEPRES-AVT-POLICY-DEFAULT id 1
      avt profile TELEPRES-AVT-POLICY-VOICE id 2
   !
   vrf customer104
      avt policy TELEPRES-AVT-POLICY
      avt profile TELEPRES-AVT-POLICY-DEFAULT id 1
      avt profile TELEPRES-AVT-POLICY-VOICE id 2
   !
   vrf customer105
      avt policy TELEPRES-AVT-POLICY
      avt profile TELEPRES-AVT-POLICY-DEFAULT id 1
      avt profile TELEPRES-AVT-POLICY-VOICE id 2
   !
   vrf customer106
      avt policy TELEPRES-AVT-POLICY
      avt profile TELEPRES-AVT-POLICY-DEFAULT id 1
      avt profile TELEPRES-AVT-POLICY-VOICE id 2
   !
   vrf customer107
      avt policy TELEPRES-AVT-POLICY
      avt profile TELEPRES-AVT-POLICY-DEFAULT id 1
      avt profile TELEPRES-AVT-POLICY-VOICE id 2
   !
   vrf customer108
      avt policy TELEPRES-AVT-POLICY
      avt profile TELEPRES-AVT-POLICY-DEFAULT id 1
      avt profile TELEPRES-AVT-POLICY-VOICE id 2
   !
   vrf customer109
      avt policy TELEPRES-AVT-POLICY
      avt profile TELEPRES-AVT-POLICY-DEFAULT id 1
      avt profile TELEPRES-AVT-POLICY-VOICE id 2
   !
   vrf customer110
      avt policy TELEPRES-AVT-POLICY
      avt profile TELEPRES-AVT-POLICY-DEFAULT id 1
      avt profile TELEPRES-AVT-POLICY-VOICE id 2
   !
   vrf customer111
      avt policy TELEPRES-AVT-POLICY
      avt profile TELEPRES-AVT-POLICY-DEFAULT id 1
      avt profile TELEPRES-AVT-POLICY-VOICE id 2
   !
   vrf customer112
      avt policy TELEPRES-AVT-POLICY
      avt profile TELEPRES-AVT-POLICY-DEFAULT id 1
      avt profile TELEPRES-AVT-POLICY-VOICE id 2
   !
   vrf customer113
      avt policy TELEPRES-AVT-POLICY
      avt profile TELEPRES-AVT-POLICY-DEFAULT id 1
      avt profile TELEPRES-AVT-POLICY-VOICE id 2
   !
   vrf customer114
      avt policy TELEPRES-AVT-POLICY
      avt profile TELEPRES-AVT-POLICY-DEFAULT id 1
      avt profile TELEPRES-AVT-POLICY-VOICE id 2
   !
   vrf customer115
      avt policy TELEPRES-AVT-POLICY
      avt profile TELEPRES-AVT-POLICY-DEFAULT id 1
      avt profile TELEPRES-AVT-POLICY-VOICE id 2
   !
   vrf customer116
      avt policy TELEPRES-AVT-POLICY
      avt profile TELEPRES-AVT-POLICY-DEFAULT id 1
      avt profile TELEPRES-AVT-POLICY-VOICE id 2
   !
   vrf customer117
      avt policy TELEPRES-AVT-POLICY
      avt profile TELEPRES-AVT-POLICY-DEFAULT id 1
      avt profile TELEPRES-AVT-POLICY-VOICE id 2
   !
   vrf customer118
      avt policy TELEPRES-AVT-POLICY
      avt profile TELEPRES-AVT-POLICY-DEFAULT id 1
      avt profile TELEPRES-AVT-POLICY-VOICE id 2
   !
   vrf customer119
      avt policy TELEPRES-AVT-POLICY
      avt profile TELEPRES-AVT-POLICY-DEFAULT id 1
      avt profile TELEPRES-AVT-POLICY-VOICE id 2
   !
   vrf customer120
      avt policy TELEPRES-AVT-POLICY
      avt profile TELEPRES-AVT-POLICY-DEFAULT id 1
      avt profile TELEPRES-AVT-POLICY-VOICE id 2
   !
   vrf customer121
      avt policy TELEPRES-AVT-POLICY
      avt profile TELEPRES-AVT-POLICY-DEFAULT id 1
      avt profile TELEPRES-AVT-POLICY-VOICE id 2
   !
   vrf customer122
      avt policy TELEPRES-AVT-POLICY
      avt profile TELEPRES-AVT-POLICY-DEFAULT id 1
      avt profile TELEPRES-AVT-POLICY-VOICE id 2
   !
   vrf customer123
      avt policy TELEPRES-AVT-POLICY
      avt profile TELEPRES-AVT-POLICY-DEFAULT id 1
      avt profile TELEPRES-AVT-POLICY-VOICE id 2
   !
   vrf customer124
      avt policy TELEPRES-AVT-POLICY
      avt profile TELEPRES-AVT-POLICY-DEFAULT id 1
      avt profile TELEPRES-AVT-POLICY-VOICE id 2
   !
   vrf customer125
      avt policy TELEPRES-AVT-POLICY
      avt profile TELEPRES-AVT-POLICY-DEFAULT id 1
      avt profile TELEPRES-AVT-POLICY-VOICE id 2
   !
   vrf customer126
      avt policy TELEPRES-AVT-POLICY
      avt profile TELEPRES-AVT-POLICY-DEFAULT id 1
      avt profile TELEPRES-AVT-POLICY-VOICE id 2
   !
   vrf customer127
      avt policy TELEPRES-AVT-POLICY
      avt profile TELEPRES-AVT-POLICY-DEFAULT id 1
      avt profile TELEPRES-AVT-POLICY-VOICE id 2
   !
   vrf customer128
      avt policy TELEPRES-AVT-POLICY
      avt profile TELEPRES-AVT-POLICY-DEFAULT id 1
      avt profile TELEPRES-AVT-POLICY-VOICE id 2
   !
   vrf customer129
      avt policy TELEPRES-AVT-POLICY
      avt profile TELEPRES-AVT-POLICY-DEFAULT id 1
      avt profile TELEPRES-AVT-POLICY-VOICE id 2
   !
   vrf customer130
      avt policy TELEPRES-AVT-POLICY
      avt profile TELEPRES-AVT-POLICY-DEFAULT id 1
      avt profile TELEPRES-AVT-POLICY-VOICE id 2
   !
   vrf customer131
      avt policy TELEPRES-AVT-POLICY
      avt profile TELEPRES-AVT-POLICY-DEFAULT id 1
      avt profile TELEPRES-AVT-POLICY-VOICE id 2
   !
   vrf customer132
      avt policy TELEPRES-AVT-POLICY
      avt profile TELEPRES-AVT-POLICY-DEFAULT id 1
      avt profile TELEPRES-AVT-POLICY-VOICE id 2
   !
   vrf customer133
      avt policy TELEPRES-AVT-POLICY
      avt profile TELEPRES-AVT-POLICY-DEFAULT id 1
      avt profile TELEPRES-AVT-POLICY-VOICE id 2
   !
   vrf customer134
      avt policy TELEPRES-AVT-POLICY
      avt profile TELEPRES-AVT-POLICY-DEFAULT id 1
      avt profile TELEPRES-AVT-POLICY-VOICE id 2
   !
   vrf customer135
      avt policy TELEPRES-AVT-POLICY
      avt profile TELEPRES-AVT-POLICY-DEFAULT id 1
      avt profile TELEPRES-AVT-POLICY-VOICE id 2
   !
   vrf customer136
      avt policy TELEPRES-AVT-POLICY
      avt profile TELEPRES-AVT-POLICY-DEFAULT id 1
      avt profile TELEPRES-AVT-POLICY-VOICE id 2
   !
   vrf customer137
      avt policy TELEPRES-AVT-POLICY
      avt profile TELEPRES-AVT-POLICY-DEFAULT id 1
      avt profile TELEPRES-AVT-POLICY-VOICE id 2
   !
   vrf customer138
      avt policy TELEPRES-AVT-POLICY
      avt profile TELEPRES-AVT-POLICY-DEFAULT id 1
      avt profile TELEPRES-AVT-POLICY-VOICE id 2
   !
   vrf customer139
      avt policy TELEPRES-AVT-POLICY
      avt profile TELEPRES-AVT-POLICY-DEFAULT id 1
      avt profile TELEPRES-AVT-POLICY-VOICE id 2
   !
   vrf customer140
      avt policy TELEPRES-AVT-POLICY
      avt profile TELEPRES-AVT-POLICY-DEFAULT id 1
      avt profile TELEPRES-AVT-POLICY-VOICE id 2
   !
   vrf customer141
      avt policy TELEPRES-AVT-POLICY
      avt profile TELEPRES-AVT-POLICY-DEFAULT id 1
      avt profile TELEPRES-AVT-POLICY-VOICE id 2
   !
   vrf customer142
      avt policy TELEPRES-AVT-POLICY
      avt profile TELEPRES-AVT-POLICY-DEFAULT id 1
      avt profile TELEPRES-AVT-POLICY-VOICE id 2
   !
   vrf customer143
      avt policy TELEPRES-AVT-POLICY
      avt profile TELEPRES-AVT-POLICY-DEFAULT id 1
      avt profile TELEPRES-AVT-POLICY-VOICE id 2
   !
   vrf customer144
      avt policy TELEPRES-AVT-POLICY
      avt profile TELEPRES-AVT-POLICY-DEFAULT id 1
      avt profile TELEPRES-AVT-POLICY-VOICE id 2
   !
   vrf customer145
      avt policy TELEPRES-AVT-POLICY
      avt profile TELEPRES-AVT-POLICY-DEFAULT id 1
      avt profile TELEPRES-AVT-POLICY-VOICE id 2
   !
   vrf customer146
      avt policy TELEPRES-AVT-POLICY
      avt profile TELEPRES-AVT-POLICY-DEFAULT id 1
      avt profile TELEPRES-AVT-POLICY-VOICE id 2
   !
   vrf customer147
      avt policy TELEPRES-AVT-POLICY
      avt profile TELEPRES-AVT-POLICY-DEFAULT id 1
      avt profile TELEPRES-AVT-POLICY-VOICE id 2
   !
   vrf customer148
      avt policy TELEPRES-AVT-POLICY
      avt profile TELEPRES-AVT-POLICY-DEFAULT id 1
      avt profile TELEPRES-AVT-POLICY-VOICE id 2
   !
   vrf customer149
      avt policy TELEPRES-AVT-POLICY
      avt profile TELEPRES-AVT-POLICY-DEFAULT id 1
      avt profile TELEPRES-AVT-POLICY-VOICE id 2
   !
   vrf customer150
      avt policy TELEPRES-AVT-POLICY
      avt profile TELEPRES-AVT-POLICY-DEFAULT id 1
      avt profile TELEPRES-AVT-POLICY-VOICE id 2
   !
   vrf customer151
      avt policy TELEPRES-AVT-POLICY
      avt profile TELEPRES-AVT-POLICY-DEFAULT id 1
      avt profile TELEPRES-AVT-POLICY-VOICE id 2
   !
   vrf customer152
      avt policy TELEPRES-AVT-POLICY
      avt profile TELEPRES-AVT-POLICY-DEFAULT id 1
      avt profile TELEPRES-AVT-POLICY-VOICE id 2
   !
   vrf customer153
      avt policy TELEPRES-AVT-POLICY
      avt profile TELEPRES-AVT-POLICY-DEFAULT id 1
      avt profile TELEPRES-AVT-POLICY-VOICE id 2
   !
   vrf customer154
      avt policy TELEPRES-AVT-POLICY
      avt profile TELEPRES-AVT-POLICY-DEFAULT id 1
      avt profile TELEPRES-AVT-POLICY-VOICE id 2
   !
   vrf customer155
      avt policy TELEPRES-AVT-POLICY
      avt profile TELEPRES-AVT-POLICY-DEFAULT id 1
      avt profile TELEPRES-AVT-POLICY-VOICE id 2
   !
   vrf customer156
      avt policy TELEPRES-AVT-POLICY
      avt profile TELEPRES-AVT-POLICY-DEFAULT id 1
      avt profile TELEPRES-AVT-POLICY-VOICE id 2
   !
   vrf customer157
      avt policy TELEPRES-AVT-POLICY
      avt profile TELEPRES-AVT-POLICY-DEFAULT id 1
      avt profile TELEPRES-AVT-POLICY-VOICE id 2
   !
   vrf customer158
      avt policy TELEPRES-AVT-POLICY
      avt profile TELEPRES-AVT-POLICY-DEFAULT id 1
      avt profile TELEPRES-AVT-POLICY-VOICE id 2
   !
   vrf customer159
      avt policy TELEPRES-AVT-POLICY
      avt profile TELEPRES-AVT-POLICY-DEFAULT id 1
      avt profile TELEPRES-AVT-POLICY-VOICE id 2
   !
   vrf customer160
      avt policy TELEPRES-AVT-POLICY
      avt profile TELEPRES-AVT-POLICY-DEFAULT id 1
      avt profile TELEPRES-AVT-POLICY-VOICE id 2
   !
   vrf customer161
      avt policy TELEPRES-AVT-POLICY
      avt profile TELEPRES-AVT-POLICY-DEFAULT id 1
      avt profile TELEPRES-AVT-POLICY-VOICE id 2
   !
   vrf customer162
      avt policy TELEPRES-AVT-POLICY
      avt profile TELEPRES-AVT-POLICY-DEFAULT id 1
      avt profile TELEPRES-AVT-POLICY-VOICE id 2
   !
   vrf customer163
      avt policy TELEPRES-AVT-POLICY
      avt profile TELEPRES-AVT-POLICY-DEFAULT id 1
      avt profile TELEPRES-AVT-POLICY-VOICE id 2
   !
   vrf customer164
      avt policy TELEPRES-AVT-POLICY
      avt profile TELEPRES-AVT-POLICY-DEFAULT id 1
      avt profile TELEPRES-AVT-POLICY-VOICE id 2
   !
   vrf customer165
      avt policy TELEPRES-AVT-POLICY
      avt profile TELEPRES-AVT-POLICY-DEFAULT id 1
      avt profile TELEPRES-AVT-POLICY-VOICE id 2
   !
   vrf customer166
      avt policy TELEPRES-AVT-POLICY
      avt profile TELEPRES-AVT-POLICY-DEFAULT id 1
      avt profile TELEPRES-AVT-POLICY-VOICE id 2
   !
   vrf customer167
      avt policy TELEPRES-AVT-POLICY
      avt profile TELEPRES-AVT-POLICY-DEFAULT id 1
      avt profile TELEPRES-AVT-POLICY-VOICE id 2
   !
   vrf customer168
      avt policy TELEPRES-AVT-POLICY
      avt profile TELEPRES-AVT-POLICY-DEFAULT id 1
      avt profile TELEPRES-AVT-POLICY-VOICE id 2
   !
   vrf customer169
      avt policy TELEPRES-AVT-POLICY
      avt profile TELEPRES-AVT-POLICY-DEFAULT id 1
      avt profile TELEPRES-AVT-POLICY-VOICE id 2
   !
   vrf customer170
      avt policy TELEPRES-AVT-POLICY
      avt profile TELEPRES-AVT-POLICY-DEFAULT id 1
      avt profile TELEPRES-AVT-POLICY-VOICE id 2
   !
   vrf customer171
      avt policy TELEPRES-AVT-POLICY
      avt profile TELEPRES-AVT-POLICY-DEFAULT id 1
      avt profile TELEPRES-AVT-POLICY-VOICE id 2
   !
   vrf customer172
      avt policy TELEPRES-AVT-POLICY
      avt profile TELEPRES-AVT-POLICY-DEFAULT id 1
      avt profile TELEPRES-AVT-POLICY-VOICE id 2
   !
   vrf customer173
      avt policy TELEPRES-AVT-POLICY
      avt profile TELEPRES-AVT-POLICY-DEFAULT id 1
      avt profile TELEPRES-AVT-POLICY-VOICE id 2
   !
   vrf customer174
      avt policy TELEPRES-AVT-POLICY
      avt profile TELEPRES-AVT-POLICY-DEFAULT id 1
      avt profile TELEPRES-AVT-POLICY-VOICE id 2
   !
   vrf customer175
      avt policy TELEPRES-AVT-POLICY
      avt profile TELEPRES-AVT-POLICY-DEFAULT id 1
      avt profile TELEPRES-AVT-POLICY-VOICE id 2
   !
   vrf customer176
      avt policy TELEPRES-AVT-POLICY
      avt profile TELEPRES-AVT-POLICY-DEFAULT id 1
      avt profile TELEPRES-AVT-POLICY-VOICE id 2
   !
   vrf customer177
      avt policy TELEPRES-AVT-POLICY
      avt profile TELEPRES-AVT-POLICY-DEFAULT id 1
      avt profile TELEPRES-AVT-POLICY-VOICE id 2
   !
   vrf customer178
      avt policy TELEPRES-AVT-POLICY
      avt profile TELEPRES-AVT-POLICY-DEFAULT id 1
      avt profile TELEPRES-AVT-POLICY-VOICE id 2
   !
   vrf customer179
      avt policy TELEPRES-AVT-POLICY
      avt profile TELEPRES-AVT-POLICY-DEFAULT id 1
      avt profile TELEPRES-AVT-POLICY-VOICE id 2
   !
   vrf customer180
      avt policy TELEPRES-AVT-POLICY
      avt profile TELEPRES-AVT-POLICY-DEFAULT id 1
      avt profile TELEPRES-AVT-POLICY-VOICE id 2
   !
   vrf customer181
      avt policy TELEPRES-AVT-POLICY
      avt profile TELEPRES-AVT-POLICY-DEFAULT id 1
      avt profile TELEPRES-AVT-POLICY-VOICE id 2
   !
   vrf customer182
      avt policy TELEPRES-AVT-POLICY
      avt profile TELEPRES-AVT-POLICY-DEFAULT id 1
      avt profile TELEPRES-AVT-POLICY-VOICE id 2
   !
   vrf customer183
      avt policy TELEPRES-AVT-POLICY
      avt profile TELEPRES-AVT-POLICY-DEFAULT id 1
      avt profile TELEPRES-AVT-POLICY-VOICE id 2
   !
   vrf customer184
      avt policy TELEPRES-AVT-POLICY
      avt profile TELEPRES-AVT-POLICY-DEFAULT id 1
      avt profile TELEPRES-AVT-POLICY-VOICE id 2
   !
   vrf customer185
      avt policy TELEPRES-AVT-POLICY
      avt profile TELEPRES-AVT-POLICY-DEFAULT id 1
      avt profile TELEPRES-AVT-POLICY-VOICE id 2
   !
   vrf customer186
      avt policy TELEPRES-AVT-POLICY
      avt profile TELEPRES-AVT-POLICY-DEFAULT id 1
      avt profile TELEPRES-AVT-POLICY-VOICE id 2
   !
   vrf customer187
      avt policy TELEPRES-AVT-POLICY
      avt profile TELEPRES-AVT-POLICY-DEFAULT id 1
      avt profile TELEPRES-AVT-POLICY-VOICE id 2
   !
   vrf customer188
      avt policy TELEPRES-AVT-POLICY
      avt profile TELEPRES-AVT-POLICY-DEFAULT id 1
      avt profile TELEPRES-AVT-POLICY-VOICE id 2
   !
   vrf customer189
      avt policy TELEPRES-AVT-POLICY
      avt profile TELEPRES-AVT-POLICY-DEFAULT id 1
      avt profile TELEPRES-AVT-POLICY-VOICE id 2
   !
   vrf customer190
      avt policy TELEPRES-AVT-POLICY
      avt profile TELEPRES-AVT-POLICY-DEFAULT id 1
      avt profile TELEPRES-AVT-POLICY-VOICE id 2
   !
   vrf customer191
      avt policy TELEPRES-AVT-POLICY
      avt profile TELEPRES-AVT-POLICY-DEFAULT id 1
      avt profile TELEPRES-AVT-POLICY-VOICE id 2
   !
   vrf customer192
      avt policy TELEPRES-AVT-POLICY
      avt profile TELEPRES-AVT-POLICY-DEFAULT id 1
      avt profile TELEPRES-AVT-POLICY-VOICE id 2
   !
   vrf customer193
      avt policy TELEPRES-AVT-POLICY
      avt profile TELEPRES-AVT-POLICY-DEFAULT id 1
      avt profile TELEPRES-AVT-POLICY-VOICE id 2
   !
   vrf customer194
      avt policy TELEPRES-AVT-POLICY
      avt profile TELEPRES-AVT-POLICY-DEFAULT id 1
      avt profile TELEPRES-AVT-POLICY-VOICE id 2
   !
   vrf customer195
      avt policy TELEPRES-AVT-POLICY
      avt profile TELEPRES-AVT-POLICY-DEFAULT id 1
      avt profile TELEPRES-AVT-POLICY-VOICE id 2
   !
   vrf customer196
      avt policy TELEPRES-AVT-POLICY
      avt profile TELEPRES-AVT-POLICY-DEFAULT id 1
      avt profile TELEPRES-AVT-POLICY-VOICE id 2
   !
   vrf customer197
      avt policy TELEPRES-AVT-POLICY
      avt profile TELEPRES-AVT-POLICY-DEFAULT id 1
      avt profile TELEPRES-AVT-POLICY-VOICE id 2
   !
   vrf customer198
      avt policy TELEPRES-AVT-POLICY
      avt profile TELEPRES-AVT-POLICY-DEFAULT id 1
      avt profile TELEPRES-AVT-POLICY-VOICE id 2
   !
   vrf customer199
      avt policy TELEPRES-AVT-POLICY
      avt profile TELEPRES-AVT-POLICY-DEFAULT id 1
      avt profile TELEPRES-AVT-POLICY-VOICE id 2
   !
   vrf customer200
      avt policy TELEPRES-AVT-POLICY
      avt profile TELEPRES-AVT-POLICY-DEFAULT id 1
      avt profile TELEPRES-AVT-POLICY-VOICE id 2
   !
   vrf customer201
      avt policy TELEPRES-AVT-POLICY
      avt profile TELEPRES-AVT-POLICY-DEFAULT id 1
      avt profile TELEPRES-AVT-POLICY-VOICE id 2
   !
   vrf customer202
      avt policy TELEPRES-AVT-POLICY
      avt profile TELEPRES-AVT-POLICY-DEFAULT id 1
      avt profile TELEPRES-AVT-POLICY-VOICE id 2
   !
   vrf customer203
      avt policy TELEPRES-AVT-POLICY
      avt profile TELEPRES-AVT-POLICY-DEFAULT id 1
      avt profile TELEPRES-AVT-POLICY-VOICE id 2
   !
   vrf customer204
      avt policy TELEPRES-AVT-POLICY
      avt profile TELEPRES-AVT-POLICY-DEFAULT id 1
      avt profile TELEPRES-AVT-POLICY-VOICE id 2
   !
   vrf customer205
      avt policy TELEPRES-AVT-POLICY
      avt profile TELEPRES-AVT-POLICY-DEFAULT id 1
      avt profile TELEPRES-AVT-POLICY-VOICE id 2
   !
   vrf customer206
      avt policy TELEPRES-AVT-POLICY
      avt profile TELEPRES-AVT-POLICY-DEFAULT id 1
      avt profile TELEPRES-AVT-POLICY-VOICE id 2
   !
   vrf customer207
      avt policy TELEPRES-AVT-POLICY
      avt profile TELEPRES-AVT-POLICY-DEFAULT id 1
      avt profile TELEPRES-AVT-POLICY-VOICE id 2
   !
   vrf customer208
      avt policy TELEPRES-AVT-POLICY
      avt profile TELEPRES-AVT-POLICY-DEFAULT id 1
      avt profile TELEPRES-AVT-POLICY-VOICE id 2
   !
   vrf customer209
      avt policy TELEPRES-AVT-POLICY
      avt profile TELEPRES-AVT-POLICY-DEFAULT id 1
      avt profile TELEPRES-AVT-POLICY-VOICE id 2
   !
   vrf customer210
      avt policy TELEPRES-AVT-POLICY
      avt profile TELEPRES-AVT-POLICY-DEFAULT id 1
      avt profile TELEPRES-AVT-POLICY-VOICE id 2
   !
   vrf customer211
      avt policy TELEPRES-AVT-POLICY
      avt profile TELEPRES-AVT-POLICY-DEFAULT id 1
      avt profile TELEPRES-AVT-POLICY-VOICE id 2
   !
   vrf customer212
      avt policy TELEPRES-AVT-POLICY
      avt profile TELEPRES-AVT-POLICY-DEFAULT id 1
      avt profile TELEPRES-AVT-POLICY-VOICE id 2
   !
   vrf customer213
      avt policy TELEPRES-AVT-POLICY
      avt profile TELEPRES-AVT-POLICY-DEFAULT id 1
      avt profile TELEPRES-AVT-POLICY-VOICE id 2
   !
   vrf customer214
      avt policy TELEPRES-AVT-POLICY
      avt profile TELEPRES-AVT-POLICY-DEFAULT id 1
      avt profile TELEPRES-AVT-POLICY-VOICE id 2
   !
   vrf customer215
      avt policy TELEPRES-AVT-POLICY
      avt profile TELEPRES-AVT-POLICY-DEFAULT id 1
      avt profile TELEPRES-AVT-POLICY-VOICE id 2
   !
   vrf customer216
      avt policy TELEPRES-AVT-POLICY
      avt profile TELEPRES-AVT-POLICY-DEFAULT id 1
      avt profile TELEPRES-AVT-POLICY-VOICE id 2
   !
   vrf customer217
      avt policy TELEPRES-AVT-POLICY
      avt profile TELEPRES-AVT-POLICY-DEFAULT id 1
      avt profile TELEPRES-AVT-POLICY-VOICE id 2
   !
   vrf customer218
      avt policy TELEPRES-AVT-POLICY
      avt profile TELEPRES-AVT-POLICY-DEFAULT id 1
      avt profile TELEPRES-AVT-POLICY-VOICE id 2
   !
   vrf customer219
      avt policy TELEPRES-AVT-POLICY
      avt profile TELEPRES-AVT-POLICY-DEFAULT id 1
      avt profile TELEPRES-AVT-POLICY-VOICE id 2
   !
   vrf customer220
      avt policy TELEPRES-AVT-POLICY
      avt profile TELEPRES-AVT-POLICY-DEFAULT id 1
      avt profile TELEPRES-AVT-POLICY-VOICE id 2
   !
   vrf customer221
      avt policy TELEPRES-AVT-POLICY
      avt profile TELEPRES-AVT-POLICY-DEFAULT id 1
      avt profile TELEPRES-AVT-POLICY-VOICE id 2
   !
   vrf customer222
      avt policy TELEPRES-AVT-POLICY
      avt profile TELEPRES-AVT-POLICY-DEFAULT id 1
      avt profile TELEPRES-AVT-POLICY-VOICE id 2
   !
   vrf customer223
      avt policy TELEPRES-AVT-POLICY
      avt profile TELEPRES-AVT-POLICY-DEFAULT id 1
      avt profile TELEPRES-AVT-POLICY-VOICE id 2
   !
   vrf customer224
      avt policy TELEPRES-AVT-POLICY
      avt profile TELEPRES-AVT-POLICY-DEFAULT id 1
      avt profile TELEPRES-AVT-POLICY-VOICE id 2
   !
   vrf customer225
      avt policy TELEPRES-AVT-POLICY
      avt profile TELEPRES-AVT-POLICY-DEFAULT id 1
      avt profile TELEPRES-AVT-POLICY-VOICE id 2
   !
   vrf customer226
      avt policy TELEPRES-AVT-POLICY
      avt profile TELEPRES-AVT-POLICY-DEFAULT id 1
      avt profile TELEPRES-AVT-POLICY-VOICE id 2
   !
   vrf customer227
      avt policy TELEPRES-AVT-POLICY
      avt profile TELEPRES-AVT-POLICY-DEFAULT id 1
      avt profile TELEPRES-AVT-POLICY-VOICE id 2
   !
   vrf customer228
      avt policy TELEPRES-AVT-POLICY
      avt profile TELEPRES-AVT-POLICY-DEFAULT id 1
      avt profile TELEPRES-AVT-POLICY-VOICE id 2
   !
   vrf customer229
      avt policy TELEPRES-AVT-POLICY
      avt profile TELEPRES-AVT-POLICY-DEFAULT id 1
      avt profile TELEPRES-AVT-POLICY-VOICE id 2
   !
   vrf customer230
      avt policy TELEPRES-AVT-POLICY
      avt profile TELEPRES-AVT-POLICY-DEFAULT id 1
      avt profile TELEPRES-AVT-POLICY-VOICE id 2
   !
   vrf customer231
      avt policy TELEPRES-AVT-POLICY
      avt profile TELEPRES-AVT-POLICY-DEFAULT id 1
      avt profile TELEPRES-AVT-POLICY-VOICE id 2
   !
   vrf customer232
      avt policy TELEPRES-AVT-POLICY
      avt profile TELEPRES-AVT-POLICY-DEFAULT id 1
      avt profile TELEPRES-AVT-POLICY-VOICE id 2
   !
   vrf customer233
      avt policy TELEPRES-AVT-POLICY
      avt profile TELEPRES-AVT-POLICY-DEFAULT id 1
      avt profile TELEPRES-AVT-POLICY-VOICE id 2
   !
   vrf customer234
      avt policy TELEPRES-AVT-POLICY
      avt profile TELEPRES-AVT-POLICY-DEFAULT id 1
      avt profile TELEPRES-AVT-POLICY-VOICE id 2
   !
   vrf customer235
      avt policy TELEPRES-AVT-POLICY
      avt profile TELEPRES-AVT-POLICY-DEFAULT id 1
      avt profile TELEPRES-AVT-POLICY-VOICE id 2
   !
   vrf customer236
      avt policy TELEPRES-AVT-POLICY
      avt profile TELEPRES-AVT-POLICY-DEFAULT id 1
      avt profile TELEPRES-AVT-POLICY-VOICE id 2
   !
   vrf customer237
      avt policy TELEPRES-AVT-POLICY
      avt profile TELEPRES-AVT-POLICY-DEFAULT id 1
      avt profile TELEPRES-AVT-POLICY-VOICE id 2
   !
   vrf customer238
      avt policy TELEPRES-AVT-POLICY
      avt profile TELEPRES-AVT-POLICY-DEFAULT id 1
      avt profile TELEPRES-AVT-POLICY-VOICE id 2
   !
   vrf customer239
      avt policy TELEPRES-AVT-POLICY
      avt profile TELEPRES-AVT-POLICY-DEFAULT id 1
      avt profile TELEPRES-AVT-POLICY-VOICE id 2
   !
   vrf customer240
      avt policy TELEPRES-AVT-POLICY
      avt profile TELEPRES-AVT-POLICY-DEFAULT id 1
      avt profile TELEPRES-AVT-POLICY-VOICE id 2
   !
   vrf customer241
      avt policy TELEPRES-AVT-POLICY
      avt profile TELEPRES-AVT-POLICY-DEFAULT id 1
      avt profile TELEPRES-AVT-POLICY-VOICE id 2
   !
   vrf customer242
      avt policy TELEPRES-AVT-POLICY
      avt profile TELEPRES-AVT-POLICY-DEFAULT id 1
      avt profile TELEPRES-AVT-POLICY-VOICE id 2
   !
   vrf customer243
      avt policy TELEPRES-AVT-POLICY
      avt profile TELEPRES-AVT-POLICY-DEFAULT id 1
      avt profile TELEPRES-AVT-POLICY-VOICE id 2
   !
   vrf customer244
      avt policy TELEPRES-AVT-POLICY
      avt profile TELEPRES-AVT-POLICY-DEFAULT id 1
      avt profile TELEPRES-AVT-POLICY-VOICE id 2
   !
   vrf customer245
      avt policy TELEPRES-AVT-POLICY
      avt profile TELEPRES-AVT-POLICY-DEFAULT id 1
      avt profile TELEPRES-AVT-POLICY-VOICE id 2
   !
   vrf customer246
      avt policy TELEPRES-AVT-POLICY
      avt profile TELEPRES-AVT-POLICY-DEFAULT id 1
      avt profile TELEPRES-AVT-POLICY-VOICE id 2
   !
   vrf customer247
      avt policy TELEPRES-AVT-POLICY
      avt profile TELEPRES-AVT-POLICY-DEFAULT id 1
      avt profile TELEPRES-AVT-POLICY-VOICE id 2
   !
   vrf customer248
      avt policy TELEPRES-AVT-POLICY
      avt profile TELEPRES-AVT-POLICY-DEFAULT id 1
      avt profile TELEPRES-AVT-POLICY-VOICE id 2
   !
   vrf customer249
      avt policy TELEPRES-AVT-POLICY
      avt profile TELEPRES-AVT-POLICY-DEFAULT id 1
      avt profile TELEPRES-AVT-POLICY-VOICE id 2
   !
   vrf customer250
      avt policy TELEPRES-AVT-POLICY
      avt profile TELEPRES-AVT-POLICY-DEFAULT id 1
      avt profile TELEPRES-AVT-POLICY-VOICE id 2
   !
   vrf customer251
      avt policy TELEPRES-AVT-POLICY
      avt profile TELEPRES-AVT-POLICY-DEFAULT id 1
      avt profile TELEPRES-AVT-POLICY-VOICE id 2
   !
   vrf customer252
      avt policy TELEPRES-AVT-POLICY
      avt profile TELEPRES-AVT-POLICY-DEFAULT id 1
      avt profile TELEPRES-AVT-POLICY-VOICE id 2
   !
   vrf customer253
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
| 65000 | 10.10.0.2 |

| BGP AS | Cluster ID |
| ------ | --------- |
| 65000 | 10.10.0.2 |

| BGP Tuning |
| ---------- |
| no bgp default ipv4-unicast |
| maximum-paths 16 |

#### Router BGP Listen Ranges

| Prefix | Peer-ID Include Router ID | Peer Group | Peer-Filter | Remote-AS | VRF |
| ------ | ------------------------- | ---------- | ----------- | --------- | --- |
| 10.0.0.0/8 | - | WAN-OVERLAY-PEERS | - | 65000 | default |

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
| Route Reflector Client | Yes |
| Source | Dps1 |
| BFD | True |
| BFD Timers | interval: 1000, min_rx: 1000, multiplier: 10 |
| TTL Max Hops | 1 |
| Send community | all |
| Maximum routes | 0 (no limit) |

##### WAN-RR-OVERLAY-PEERS

| Settings | Value |
| -------- | ----- |
| Address Family | wan |
| Remote AS | 65000 |
| Route Reflector Client | Yes |
| Source | Dps1 |
| BFD | True |
| BFD Timers | interval: 1000, min_rx: 1000, multiplier: 10 |
| TTL Max Hops | 1 |
| Send community | all |
| Maximum routes | 0 (no limit) |

#### BGP Neighbors

| Neighbor | Remote AS | VRF | Shutdown | Send-community | Maximum-routes | Allowas-in | BFD | RIB Pre-Policy Retain | Route-Reflector Client | Passive | TTL Max Hops |
| -------- | --------- | --- | -------- | -------------- | -------------- | ---------- | --- | --------------------- | ---------------------- | ------- | ------------ |
| 10.255.0.1 | Inherited from peer group WAN-RR-OVERLAY-PEERS | default | - | Inherited from peer group WAN-RR-OVERLAY-PEERS | Inherited from peer group WAN-RR-OVERLAY-PEERS | - | Inherited from peer group WAN-RR-OVERLAY-PEERS(interval: 1000, min_rx: 1000, multiplier: 10) | - | Inherited from peer group WAN-RR-OVERLAY-PEERS | - | Inherited from peer group WAN-RR-OVERLAY-PEERS |

#### Router BGP EVPN Address Family

- Next-hop resolution is **disabled**

##### EVPN Peer Groups

| Peer Group | Activate | Encapsulation |
| ---------- | -------- | ------------- |
| WAN-OVERLAY-PEERS | True | default |
| WAN-RR-OVERLAY-PEERS | True | default |

#### Router BGP IPv4 SR-TE Address Family

##### IPv4 SR-TE Peer Groups

| Peer Group | Activate | Route-map In | Route-map Out |
| ---------- | -------- | ------------ | ------------- |
| WAN-OVERLAY-PEERS | True | - | - |
| WAN-RR-OVERLAY-PEERS | True | - | - |

#### Router BGP Link-State Address Family

##### Link-State Peer Groups

| Peer Group | Activate | Missing policy In action | Missing policy Out action |
| ---------- | -------- | ------------------------ | ------------------------- |
| WAN-OVERLAY-PEERS | True | - | deny |
| WAN-RR-OVERLAY-PEERS | True | - | - |

##### Link-State Path Selection Configuration

| Settings | Value |
| -------- | ----- |
| Role(s) | consumer<br>propagator |

#### Router BGP Path-Selection Address Family

##### Path-Selection Peer Groups

| Peer Group | Activate |
| ---------- | -------- |
| WAN-OVERLAY-PEERS | True |
| WAN-RR-OVERLAY-PEERS | True |

#### Router BGP VRFs

| VRF | Route-Distinguisher | Redistribute |
| --- | ------------------- | ------------ |
| default | 10.10.0.2:1 | - |

#### Router BGP Device Configuration

```eos
!
router bgp 65000
   router-id 10.10.0.2
   maximum-paths 16
   no bgp default ipv4-unicast
   bgp cluster-id 10.10.0.2
   bgp listen range 10.0.0.0/8 peer-group WAN-OVERLAY-PEERS remote-as 65000
   neighbor IPv4-UNDERLAY-PEERS peer group
   neighbor IPv4-UNDERLAY-PEERS send-community
   neighbor IPv4-UNDERLAY-PEERS maximum-routes 12000
   neighbor IPv4-UNDERLAY-PEERS route-map RM-BGP-UNDERLAY-PEERS-IN in
   neighbor WAN-OVERLAY-PEERS peer group
   neighbor WAN-OVERLAY-PEERS remote-as 65000
   neighbor WAN-OVERLAY-PEERS update-source Dps1
   neighbor WAN-OVERLAY-PEERS route-reflector-client
   neighbor WAN-OVERLAY-PEERS bfd
   neighbor WAN-OVERLAY-PEERS bfd interval 1000 min-rx 1000 multiplier 10
   neighbor WAN-OVERLAY-PEERS ttl maximum-hops 1
   neighbor WAN-OVERLAY-PEERS password 7 <removed>
   neighbor WAN-OVERLAY-PEERS send-community
   neighbor WAN-OVERLAY-PEERS maximum-routes 0
   neighbor WAN-RR-OVERLAY-PEERS peer group
   neighbor WAN-RR-OVERLAY-PEERS remote-as 65000
   neighbor WAN-RR-OVERLAY-PEERS update-source Dps1
   neighbor WAN-RR-OVERLAY-PEERS route-reflector-client
   neighbor WAN-RR-OVERLAY-PEERS bfd
   neighbor WAN-RR-OVERLAY-PEERS bfd interval 1000 min-rx 1000 multiplier 10
   neighbor WAN-RR-OVERLAY-PEERS ttl maximum-hops 1
   neighbor WAN-RR-OVERLAY-PEERS send-community
   neighbor WAN-RR-OVERLAY-PEERS maximum-routes 0
   neighbor 10.255.0.1 peer group WAN-RR-OVERLAY-PEERS
   neighbor 10.255.0.1 description pf1
   redistribute connected route-map RM-CONN-2-BGP
   !
   address-family evpn
      neighbor WAN-OVERLAY-PEERS activate
      neighbor WAN-RR-OVERLAY-PEERS activate
      next-hop resolution disabled
   !
   address-family ipv4
      neighbor IPv4-UNDERLAY-PEERS activate
      no neighbor WAN-OVERLAY-PEERS activate
      no neighbor WAN-RR-OVERLAY-PEERS activate
   !
   address-family ipv4 sr-te
      neighbor WAN-OVERLAY-PEERS activate
      neighbor WAN-RR-OVERLAY-PEERS activate
   !
   address-family link-state
      neighbor WAN-OVERLAY-PEERS activate
      neighbor WAN-OVERLAY-PEERS missing-policy direction out action deny
      neighbor WAN-RR-OVERLAY-PEERS activate
      path-selection role consumer propagator
   !
   address-family path-selection
      bgp additional-paths receive
      bgp additional-paths send any
      neighbor WAN-OVERLAY-PEERS activate
      neighbor WAN-RR-OVERLAY-PEERS activate
   !
   vrf default
      rd 10.10.0.2:1
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
| 10 | permit 10.10.0.0/30 eq 32 |

#### Prefix-lists Device Configuration

```eos
!
ip prefix-list PL-LOOPBACKS-EVPN-OVERLAY
   seq 10 permit 10.10.0.0/30 eq 32
```

### Route-maps

#### Route-maps Summary

##### RM-CONN-2-BGP

| Sequence | Type | Match | Set | Sub-Route-Map | Continue |
| -------- | ---- | ----- | --- | ------------- | -------- |
| 10 | permit | ip address prefix-list PL-LOOPBACKS-EVPN-OVERLAY | extcommunity soo 10.10.0.2:0 additive | - | - |

##### RM-EVPN-EXPORT-VRF-DEFAULT

| Sequence | Type | Match | Set | Sub-Route-Map | Continue |
| -------- | ---- | ----- | --- | ------------- | -------- |
| 10 | permit | extcommunity ECL-EVPN-SOO | - | - | - |

#### Route-maps Device Configuration

```eos
!
route-map RM-CONN-2-BGP permit 10
   match ip address prefix-list PL-LOOPBACKS-EVPN-OVERLAY
   set extcommunity soo 10.10.0.2:0 additive
!
route-map RM-EVPN-EXPORT-VRF-DEFAULT permit 10
   match extcommunity ECL-EVPN-SOO
```

### IP Extended Community Lists

#### IP Extended Community Lists Summary

| List Name | Type | Extended Communities |
| --------- | ---- | -------------------- |
| ECL-EVPN-SOO | permit | soo 10.10.0.2:0 |

#### IP Extended Community Lists Device Configuration

```eos
!
ip extcommunity-list ECL-EVPN-SOO permit soo 10.10.0.2:0
```

## VRF Instances

### VRF Instances Summary

| VRF Name | IP Routing |
| -------- | ---------- |
| MGMT | disabled |

### VRF Instances Device Configuration

```eos
!
vrf instance MGMT
```

## Platform

### Platform Summary

#### Platform Software Forwarding Engine Summary

| Settings | Value |
| -------- | ----- |
| Maximum CPU Allocation | 1 |

### Platform Device Configuration

```eos
!
platform sfe data-plane cpu allocation maximum 1
```

## Application Traffic Recognition

### Applications

#### IPv4 Applications

| Name | Source Prefix | Destination Prefix | Protocols | Protocol Ranges | TCP Source Port Set | TCP Destination Port Set | UDP Source Port Set | UDP Destination Port Set |
| ---- | ------------- | ------------------ | --------- | --------------- | ------------------- | ------------------------ | ------------------- | ------------------------ |
| APP-CONTROL-PLANE | PFX-LOCAL-VTEP-IP | - | - | - | - | - | - | - |
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
| PFX-LOCAL-VTEP-IP | 10.255.0.2/32 |

### Router Application-Traffic-Recognition Device Configuration

```eos
!
application traffic recognition
   !
   application ipv4 APP-CONTROL-PLANE
      source prefix field-set PFX-LOCAL-VTEP-IP
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
   field-set ipv4 prefix PFX-LOCAL-VTEP-IP
      10.255.0.2/32
   !
   field-set l4-port RTP_PORT
      1-65000
```

### Router Path-selection

#### Router Path-selection Summary

| Setting | Value |
| ------  | ----- |
| Dynamic peers source | STUN |

#### TCP MSS Ceiling Configuration

| IPV4 segment size | Direction |
| ----------------- | --------- |
| auto | ingress |

#### Path Groups

##### Path Group gmpls1

| Setting | Value |
| ------  | ----- |
| Path Group ID | 111 |
| IPSec profile | CP-PROFILE |

###### Local Interfaces

| Interface name | Public address | STUN server profile(s) |
| -------------- | -------------- | ---------------------- |
| Ethernet1/1 | - |  |

###### Static Peers

| Router IP | Name | IPv4 address(es) |
| --------- | ---- | ---------------- |
| 10.255.0.1 | pf1 | 172.16.51.2 |

##### Path Group gmpls2

| Setting | Value |
| ------  | ----- |
| Path Group ID | 112 |
| IPSec profile | CP-PROFILE |

###### Local Interfaces

| Interface name | Public address | STUN server profile(s) |
| -------------- | -------------- | ---------------------- |
| Ethernet1/2 | - |  |

###### Static Peers

| Router IP | Name | IPv4 address(es) |
| --------- | ---- | ---------------- |
| 10.255.0.1 | pf1 | 172.16.51.6 |

##### Path Group LAN_HA

| Setting | Value |
| ------  | ----- |
| Path Group ID | 65535 |
| Flow assignment | LAN |

##### Path Group rmpls1

| Setting | Value |
| ------  | ----- |
| Path Group ID | 101 |
| IPSec profile | CP-PROFILE |

###### Local Interfaces

| Interface name | Public address | STUN server profile(s) |
| -------------- | -------------- | ---------------------- |
| Ethernet1/5 | - |  |

###### Static Peers

| Router IP | Name | IPv4 address(es) |
| --------- | ---- | ---------------- |
| 10.255.0.1 | pf1 | 172.16.51.10 |

##### Path Group rmpls2

| Setting | Value |
| ------  | ----- |
| Path Group ID | 102 |
| IPSec profile | CP-PROFILE |

###### Local Interfaces

| Interface name | Public address | STUN server profile(s) |
| -------------- | -------------- | ---------------------- |
| Ethernet1/6 | - |  |

###### Static Peers

| Router IP | Name | IPv4 address(es) |
| --------- | ---- | ---------------- |
| 10.255.0.1 | pf1 | 172.16.51.14 |

##### Path Group rmpls3

| Setting | Value |
| ------  | ----- |
| Path Group ID | 103 |
| IPSec profile | CP-PROFILE |
| Keepalive interval(failure threshold) | 50(3) |

###### Local Interfaces

| Interface name | Public address | STUN server profile(s) |
| -------------- | -------------- | ---------------------- |
| Ethernet1/7 | - |  |

###### Static Peers

| Router IP | Name | IPv4 address(es) |
| --------- | ---- | ---------------- |
| 10.255.0.1 | pf1 | 172.16.51.18 |

##### Path Group rmpls4

| Setting | Value |
| ------  | ----- |
| Path Group ID | 104 |
| IPSec profile | CP-PROFILE |

###### Local Interfaces

| Interface name | Public address | STUN server profile(s) |
| -------------- | -------------- | ---------------------- |
| Ethernet1/8 | - |  |

###### Static Peers

| Router IP | Name | IPv4 address(es) |
| --------- | ---- | ---------------- |
| 10.255.0.1 | pf1 | 172.16.51.22 |

#### Load-balance Policies

| Policy Name | Jitter (ms) | Latency (ms) | Loss Rate (%) | Path Groups (priority) | Lowest Hop Count |
| ----------- | ----------- | ------------ | ------------- | ---------------------- | ---------------- |
| LB-DEFAULT-AVT-POLICY-CONTROL-PLANE | - | - | - | gmpls1 (1)<br>gmpls2 (1)<br>LAN_HA (1)<br>rmpls1 (1)<br>rmpls2 (1)<br>rmpls3 (1)<br>rmpls4 (1) | False |
| LB-DEFAULT-AVT-POLICY-DEFAULT | - | - | - | gmpls1 (1)<br>gmpls2 (1)<br>LAN_HA (1)<br>rmpls1 (1)<br>rmpls2 (1)<br>rmpls3 (1)<br>rmpls4 (1) | False |
| LB-TELEPRES-AVT-POLICY-DEFAULT | - | - | - | gmpls1 (1)<br>gmpls2 (1)<br>LAN_HA (1)<br>rmpls1 (1)<br>rmpls2 (1)<br>rmpls3 (1)<br>rmpls4 (1) | False |
| LB-TELEPRES-AVT-POLICY-VOICE | 20 | 120 | 0.3 | gmpls1 (1)<br>gmpls2 (1)<br>LAN_HA (1)<br>rmpls1 (1)<br>rmpls2 (1)<br>rmpls3 (1)<br>rmpls4 (1) | True |

#### Router Path-selection Device Configuration

```eos
!
router path-selection
   peer dynamic source stun
   tcp mss ceiling ipv4 ingress
   !
   path-group gmpls1 id 111
      ipsec profile CP-PROFILE
      !
      local interface Ethernet1/1
      !
      peer static router-ip 10.255.0.1
         name pf1
         ipv4 address 172.16.51.2
   !
   path-group gmpls2 id 112
      ipsec profile CP-PROFILE
      !
      local interface Ethernet1/2
      !
      peer static router-ip 10.255.0.1
         name pf1
         ipv4 address 172.16.51.6
   !
   path-group LAN_HA id 65535
      flow assignment lan
   !
   path-group rmpls1 id 101
      ipsec profile CP-PROFILE
      !
      local interface Ethernet1/5
      !
      peer static router-ip 10.255.0.1
         name pf1
         ipv4 address 172.16.51.10
   !
   path-group rmpls2 id 102
      ipsec profile CP-PROFILE
      !
      local interface Ethernet1/6
      !
      peer static router-ip 10.255.0.1
         name pf1
         ipv4 address 172.16.51.14
   !
   path-group rmpls3 id 103
      ipsec profile CP-PROFILE
      keepalive interval 50 milliseconds failure-threshold 3 intervals
      !
      local interface Ethernet1/7
      !
      peer static router-ip 10.255.0.1
         name pf1
         ipv4 address 172.16.51.18
   !
   path-group rmpls4 id 104
      ipsec profile CP-PROFILE
      !
      local interface Ethernet1/8
      !
      peer static router-ip 10.255.0.1
         name pf1
         ipv4 address 172.16.51.22
   !
   load-balance policy LB-DEFAULT-AVT-POLICY-CONTROL-PLANE
      path-group gmpls1
      path-group gmpls2
      path-group LAN_HA
      path-group rmpls1
      path-group rmpls2
      path-group rmpls3
      path-group rmpls4
   !
   load-balance policy LB-DEFAULT-AVT-POLICY-DEFAULT
      path-group gmpls1
      path-group gmpls2
      path-group LAN_HA
      path-group rmpls1
      path-group rmpls2
      path-group rmpls3
      path-group rmpls4
   !
   load-balance policy LB-TELEPRES-AVT-POLICY-DEFAULT
      path-group gmpls1
      path-group gmpls2
      path-group LAN_HA
      path-group rmpls1
      path-group rmpls2
      path-group rmpls3
      path-group rmpls4
   !
   load-balance policy LB-TELEPRES-AVT-POLICY-VOICE
      hop count lowest
      jitter 20
      latency 120
      loss-rate 0.3
      path-group gmpls1
      path-group gmpls2
      path-group LAN_HA
      path-group rmpls1
      path-group rmpls2
      path-group rmpls3
      path-group rmpls4
```

## STUN

### STUN Server

| Server Local Interfaces | Bindings Timeout (s) | SSL Profile | SSL Connection Lifetime | Port |
| ----------------------- | -------------------- | ----------- | ----------------------- | ---- |
| Ethernet1/1<br>Ethernet1/2<br>Ethernet1/5<br>Ethernet1/6<br>Ethernet1/7<br>Ethernet1/8 | - | - | - | 3478 |

### STUN Device Configuration

```eos
!
stun
   server
      local-interface Ethernet1/1
      local-interface Ethernet1/2
      local-interface Ethernet1/5
      local-interface Ethernet1/6
      local-interface Ethernet1/7
      local-interface Ethernet1/8
```
