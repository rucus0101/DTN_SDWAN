# SITE1

## Table of Contents

- [Fabric Switches and Management IP](#fabric-switches-and-management-ip)
  - [Fabric Switches with inband Management IP](#fabric-switches-with-inband-management-ip)
- [Fabric Topology](#fabric-topology)
- [Fabric IP Allocation](#fabric-ip-allocation)
  - [Fabric Point-To-Point Links](#fabric-point-to-point-links)
  - [Point-To-Point Links Node Allocation](#point-to-point-links-node-allocation)
  - [Loopback Interfaces (BGP EVPN Peering)](#loopback-interfaces-bgp-evpn-peering)
  - [Loopback0 Interfaces Node Allocation](#loopback0-interfaces-node-allocation)
  - [VTEP Loopback VXLAN Tunnel Source Interfaces (VTEPs Only)](#vtep-loopback-vxlan-tunnel-source-interfaces-vteps-only)
  - [VTEP Loopback Node allocation](#vtep-loopback-node-allocation)

## Fabric Switches and Management IP

| POD | Type | Node | Management IP | Platform | Provisioned in CloudVision | Serial Number |
| --- | ---- | ---- | ------------- | -------- | -------------------------- | ------------- |
| SITE1 | l3leaf | border1-site1 | 192.168.66.107/24 | cEOSLab | Provisioned | - |
| SITE1 | l3leaf | border2-site1 | 192.168.66.108/24 | cEOSLab | Provisioned | - |
| SITE1 | l3leaf | leaf1-site1 | 192.168.66.111/24 | cEOSLab | Provisioned | - |
| SITE1 | l3leaf | leaf2-site1 | 192.168.66.112/24 | cEOSLab | Provisioned | - |
| SITE1 | spine | spine1-site1 | 192.168.66.109/24 | cEOSLab | Provisioned | - |
| SITE1 | spine | spine2-site1 | 192.168.66.110/24 | cEOSLab | Provisioned | - |
| SITE1 | wan_router | wan1-site1 | 192.168.66.23/24 | - | Provisioned | - |
| SITE1 | wan_router | wan2-site1 | 192.168.66.24/24 | - | Provisioned | - |

> Provision status is based on Ansible inventory declaration and do not represent real status from CloudVision.

### Fabric Switches with inband Management IP

| POD | Type | Node | Management IP | Inband Interface |
| --- | ---- | ---- | ------------- | ---------------- |

## Fabric Topology

| Type | Node | Node Interface | Peer Type | Peer Node | Peer Interface |
| ---- | ---- | -------------- | --------- | ----------| -------------- |
| l3leaf | border1-site1 | Ethernet1 | spine | spine1-site1 | Ethernet1 |
| l3leaf | border1-site1 | Ethernet2 | spine | spine2-site1 | Ethernet1 |
| l3leaf | border1-site1 | Ethernet3 | wan_router | wan1-site1 | Ethernet1 |
| l3leaf | border1-site1 | Ethernet3.10 | wan_router | wan1-site1 | Ethernet1.10 |
| l3leaf | border1-site1 | Ethernet3.20 | wan_router | wan1-site1 | Ethernet1.20 |
| l3leaf | border1-site1 | Ethernet4 | wan_router | wan2-site1 | Ethernet1 |
| l3leaf | border1-site1 | Ethernet4.10 | wan_router | wan2-site1 | Ethernet1.10 |
| l3leaf | border1-site1 | Ethernet4.20 | wan_router | wan2-site1 | Ethernet1.20 |
| l3leaf | border1-site1 | Ethernet5 | mlag_peer | border2-site1 | Ethernet5 |
| l3leaf | border1-site1 | Ethernet6 | mlag_peer | border2-site1 | Ethernet6 |
| l3leaf | border2-site1 | Ethernet1 | spine | spine1-site1 | Ethernet2 |
| l3leaf | border2-site1 | Ethernet2 | spine | spine2-site1 | Ethernet2 |
| l3leaf | border2-site1 | Ethernet3 | wan_router | wan1-site1 | Ethernet2 |
| l3leaf | border2-site1 | Ethernet3.10 | wan_router | wan1-site1 | Ethernet2.10 |
| l3leaf | border2-site1 | Ethernet3.20 | wan_router | wan1-site1 | Ethernet2.20 |
| l3leaf | border2-site1 | Ethernet4 | wan_router | wan2-site1 | Ethernet2 |
| l3leaf | border2-site1 | Ethernet4.10 | wan_router | wan2-site1 | Ethernet2.10 |
| l3leaf | border2-site1 | Ethernet4.20 | wan_router | wan2-site1 | Ethernet2.20 |
| l3leaf | leaf1-site1 | Ethernet1 | spine | spine1-site1 | Ethernet3 |
| l3leaf | leaf1-site1 | Ethernet2 | spine | spine2-site1 | Ethernet3 |
| l3leaf | leaf1-site1 | Ethernet5 | mlag_peer | leaf2-site1 | Ethernet5 |
| l3leaf | leaf1-site1 | Ethernet6 | mlag_peer | leaf2-site1 | Ethernet6 |
| l3leaf | leaf2-site1 | Ethernet1 | spine | spine1-site1 | Ethernet4 |
| l3leaf | leaf2-site1 | Ethernet2 | spine | spine2-site1 | Ethernet4 |

## Fabric IP Allocation

### Fabric Point-To-Point Links

| Uplink IPv4 Pool | Available Addresses | Assigned addresses | Assigned Address % |
| ---------------- | ------------------- | ------------------ | ------------------ |
| 10.101.0.0/25 | 128 | 16 | 12.5 % |
| 10.101.0.128/25 | 128 | 24 | 18.75 % |

### Point-To-Point Links Node Allocation

| Node | Node Interface | Node IP Address | Peer Node | Peer Interface | Peer IP Address |
| ---- | -------------- | --------------- | --------- | -------------- | --------------- |
| border1-site1 | Ethernet1 | 10.101.0.1/31 | spine1-site1 | Ethernet1 | 10.101.0.0/31 |
| border1-site1 | Ethernet2 | 10.101.0.3/31 | spine2-site1 | Ethernet1 | 10.101.0.2/31 |
| border1-site1 | Ethernet3 | 10.101.0.128/31 | wan1-site1 | Ethernet1 | 10.101.0.129/31 |
| border1-site1 | Ethernet3.10 | 10.101.0.128/31 | wan1-site1 | Ethernet1.10 | 10.101.0.129/31 |
| border1-site1 | Ethernet3.20 | 10.101.0.128/31 | wan1-site1 | Ethernet1.20 | 10.101.0.129/31 |
| border1-site1 | Ethernet4 | 10.101.0.132/31 | wan2-site1 | Ethernet1 | 10.101.0.133/31 |
| border1-site1 | Ethernet4.10 | 10.101.0.132/31 | wan2-site1 | Ethernet1.10 | 10.101.0.133/31 |
| border1-site1 | Ethernet4.20 | 10.101.0.132/31 | wan2-site1 | Ethernet1.20 | 10.101.0.133/31 |
| border2-site1 | Ethernet1 | 10.101.0.5/31 | spine1-site1 | Ethernet2 | 10.101.0.4/31 |
| border2-site1 | Ethernet2 | 10.101.0.7/31 | spine2-site1 | Ethernet2 | 10.101.0.6/31 |
| border2-site1 | Ethernet3 | 10.101.0.130/31 | wan1-site1 | Ethernet2 | 10.101.0.131/31 |
| border2-site1 | Ethernet3.10 | 10.101.0.130/31 | wan1-site1 | Ethernet2.10 | 10.101.0.131/31 |
| border2-site1 | Ethernet3.20 | 10.101.0.130/31 | wan1-site1 | Ethernet2.20 | 10.101.0.131/31 |
| border2-site1 | Ethernet4 | 10.101.0.134/31 | wan2-site1 | Ethernet2 | 10.101.0.135/31 |
| border2-site1 | Ethernet4.10 | 10.101.0.134/31 | wan2-site1 | Ethernet2.10 | 10.101.0.135/31 |
| border2-site1 | Ethernet4.20 | 10.101.0.134/31 | wan2-site1 | Ethernet2.20 | 10.101.0.135/31 |
| leaf1-site1 | Ethernet1 | 10.101.0.9/31 | spine1-site1 | Ethernet3 | 10.101.0.8/31 |
| leaf1-site1 | Ethernet2 | 10.101.0.11/31 | spine2-site1 | Ethernet3 | 10.101.0.10/31 |
| leaf2-site1 | Ethernet1 | 10.101.0.13/31 | spine1-site1 | Ethernet4 | 10.101.0.12/31 |
| leaf2-site1 | Ethernet2 | 10.101.0.15/31 | spine2-site1 | Ethernet4 | 10.101.0.14/31 |

### Loopback Interfaces (BGP EVPN Peering)

| Loopback Pool | Available Addresses | Assigned addresses | Assigned Address % |
| ------------- | ------------------- | ------------------ | ------------------ |
| 10.101.1.0/24 | 256 | 6 | 2.35 % |
| 10.101.1.0/25 | 128 | 6 | 4.69 % |
| 10.101.254.0/25 | 128 | 2 | 1.57 % |

### Loopback0 Interfaces Node Allocation

| POD | Node | Loopback0 |
| --- | ---- | --------- |
| SITE1 | border1-site1 | 10.101.1.3/32 |
| SITE1 | border2-site1 | 10.101.1.4/32 |
| SITE1 | leaf1-site1 | 10.101.1.5/32 |
| SITE1 | leaf2-site1 | 10.101.1.6/32 |
| SITE1 | spine1-site1 | 10.101.1.1/32 |
| SITE1 | spine2-site1 | 10.101.1.2/32 |
| SITE1 | wan1-site1 | 10.101.254.1/32 |
| SITE1 | wan2-site1 | 10.101.254.2/32 |

### VTEP Loopback VXLAN Tunnel Source Interfaces (VTEPs Only)

| VTEP Loopback Pool | Available Addresses | Assigned addresses | Assigned Address % |
| --------------------- | ------------------- | ------------------ | ------------------ |
| 10.101.2.0/25 | 128 | 4 | 3.13 % |
| 10.101.255.0/25 | 128 | 0 | 0.0 % |

### VTEP Loopback Node allocation

| POD | Node | Loopback1 |
| --- | ---- | --------- |
| SITE1 | border1-site1 | 10.101.2.3/32 |
| SITE1 | border2-site1 | 10.101.2.3/32 |
| SITE1 | leaf1-site1 | 10.101.2.5/32 |
| SITE1 | leaf2-site1 | 10.101.2.5/32 |
