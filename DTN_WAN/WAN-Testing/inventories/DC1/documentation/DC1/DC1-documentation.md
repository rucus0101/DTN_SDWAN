# DC1

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
| DC1 | l3leaf | border1-dc1 | 192.168.66.101/24 | cEOSLab | Provisioned | - |
| DC1 | l3leaf | border2-dc1 | 192.168.66.102/24 | cEOSLab | Provisioned | - |
| DC1 | l3leaf | leaf1-dc1 | 192.168.66.105/24 | cEOSLab | Provisioned | - |
| DC1 | l3leaf | leaf2-dc1 | 192.168.66.106/24 | cEOSLab | Provisioned | - |
| DC1 | spine | spine1-dc1 | 192.168.66.103/24 | cEOSLab | Provisioned | - |
| DC1 | spine | spine2-dc1 | 192.168.66.104/24 | cEOSLab | Provisioned | - |
| DC1 | wan_router | wan1-dc1 | 192.168.66.21/24 | - | Provisioned | - |
| DC1 | wan_router | wan2-dc1 | 192.168.66.22/24 | - | Provisioned | - |

> Provision status is based on Ansible inventory declaration and do not represent real status from CloudVision.

### Fabric Switches with inband Management IP

| POD | Type | Node | Management IP | Inband Interface |
| --- | ---- | ---- | ------------- | ---------------- |

## Fabric Topology

| Type | Node | Node Interface | Peer Type | Peer Node | Peer Interface |
| ---- | ---- | -------------- | --------- | ----------| -------------- |
| l3leaf | border1-dc1 | Ethernet1 | spine | spine1-dc1 | Ethernet1 |
| l3leaf | border1-dc1 | Ethernet2 | spine | spine2-dc1 | Ethernet1 |
| l3leaf | border1-dc1 | Ethernet3 | wan_router | wan1-dc1 | Ethernet1 |
| l3leaf | border1-dc1 | Ethernet3.10 | wan_router | wan1-dc1 | Ethernet1.10 |
| l3leaf | border1-dc1 | Ethernet3.20 | wan_router | wan1-dc1 | Ethernet1.20 |
| l3leaf | border1-dc1 | Ethernet4 | wan_router | wan2-dc1 | Ethernet1 |
| l3leaf | border1-dc1 | Ethernet4.10 | wan_router | wan2-dc1 | Ethernet1.10 |
| l3leaf | border1-dc1 | Ethernet4.20 | wan_router | wan2-dc1 | Ethernet1.20 |
| l3leaf | border1-dc1 | Ethernet5 | mlag_peer | border2-dc1 | Ethernet5 |
| l3leaf | border1-dc1 | Ethernet6 | mlag_peer | border2-dc1 | Ethernet6 |
| l3leaf | border2-dc1 | Ethernet1 | spine | spine1-dc1 | Ethernet2 |
| l3leaf | border2-dc1 | Ethernet2 | spine | spine2-dc1 | Ethernet2 |
| l3leaf | border2-dc1 | Ethernet3 | wan_router | wan1-dc1 | Ethernet2 |
| l3leaf | border2-dc1 | Ethernet3.10 | wan_router | wan1-dc1 | Ethernet2.10 |
| l3leaf | border2-dc1 | Ethernet3.20 | wan_router | wan1-dc1 | Ethernet2.20 |
| l3leaf | border2-dc1 | Ethernet4 | wan_router | wan2-dc1 | Ethernet2 |
| l3leaf | border2-dc1 | Ethernet4.10 | wan_router | wan2-dc1 | Ethernet2.10 |
| l3leaf | border2-dc1 | Ethernet4.20 | wan_router | wan2-dc1 | Ethernet2.20 |
| l3leaf | leaf1-dc1 | Ethernet1 | spine | spine1-dc1 | Ethernet3 |
| l3leaf | leaf1-dc1 | Ethernet2 | spine | spine2-dc1 | Ethernet3 |
| l3leaf | leaf1-dc1 | Ethernet5 | mlag_peer | leaf2-dc1 | Ethernet5 |
| l3leaf | leaf1-dc1 | Ethernet6 | mlag_peer | leaf2-dc1 | Ethernet6 |
| l3leaf | leaf2-dc1 | Ethernet1 | spine | spine1-dc1 | Ethernet4 |
| l3leaf | leaf2-dc1 | Ethernet2 | spine | spine2-dc1 | Ethernet4 |

## Fabric IP Allocation

### Fabric Point-To-Point Links

| Uplink IPv4 Pool | Available Addresses | Assigned addresses | Assigned Address % |
| ---------------- | ------------------- | ------------------ | ------------------ |
| 10.100.0.0/25 | 128 | 16 | 12.5 % |
| 10.100.0.128/25 | 128 | 24 | 18.75 % |

### Point-To-Point Links Node Allocation

| Node | Node Interface | Node IP Address | Peer Node | Peer Interface | Peer IP Address |
| ---- | -------------- | --------------- | --------- | -------------- | --------------- |
| border1-dc1 | Ethernet1 | 10.100.0.1/31 | spine1-dc1 | Ethernet1 | 10.100.0.0/31 |
| border1-dc1 | Ethernet2 | 10.100.0.3/31 | spine2-dc1 | Ethernet1 | 10.100.0.2/31 |
| border1-dc1 | Ethernet3 | 10.100.0.128/31 | wan1-dc1 | Ethernet1 | 10.100.0.129/31 |
| border1-dc1 | Ethernet3.10 | 10.100.0.128/31 | wan1-dc1 | Ethernet1.10 | 10.100.0.129/31 |
| border1-dc1 | Ethernet3.20 | 10.100.0.128/31 | wan1-dc1 | Ethernet1.20 | 10.100.0.129/31 |
| border1-dc1 | Ethernet4 | 10.100.0.132/31 | wan2-dc1 | Ethernet1 | 10.100.0.133/31 |
| border1-dc1 | Ethernet4.10 | 10.100.0.132/31 | wan2-dc1 | Ethernet1.10 | 10.100.0.133/31 |
| border1-dc1 | Ethernet4.20 | 10.100.0.132/31 | wan2-dc1 | Ethernet1.20 | 10.100.0.133/31 |
| border2-dc1 | Ethernet1 | 10.100.0.5/31 | spine1-dc1 | Ethernet2 | 10.100.0.4/31 |
| border2-dc1 | Ethernet2 | 10.100.0.7/31 | spine2-dc1 | Ethernet2 | 10.100.0.6/31 |
| border2-dc1 | Ethernet3 | 10.100.0.130/31 | wan1-dc1 | Ethernet2 | 10.100.0.131/31 |
| border2-dc1 | Ethernet3.10 | 10.100.0.130/31 | wan1-dc1 | Ethernet2.10 | 10.100.0.131/31 |
| border2-dc1 | Ethernet3.20 | 10.100.0.130/31 | wan1-dc1 | Ethernet2.20 | 10.100.0.131/31 |
| border2-dc1 | Ethernet4 | 10.100.0.134/31 | wan2-dc1 | Ethernet2 | 10.100.0.135/31 |
| border2-dc1 | Ethernet4.10 | 10.100.0.134/31 | wan2-dc1 | Ethernet2.10 | 10.100.0.135/31 |
| border2-dc1 | Ethernet4.20 | 10.100.0.134/31 | wan2-dc1 | Ethernet2.20 | 10.100.0.135/31 |
| leaf1-dc1 | Ethernet1 | 10.100.0.9/31 | spine1-dc1 | Ethernet3 | 10.100.0.8/31 |
| leaf1-dc1 | Ethernet2 | 10.100.0.11/31 | spine2-dc1 | Ethernet3 | 10.100.0.10/31 |
| leaf2-dc1 | Ethernet1 | 10.100.0.13/31 | spine1-dc1 | Ethernet4 | 10.100.0.12/31 |
| leaf2-dc1 | Ethernet2 | 10.100.0.15/31 | spine2-dc1 | Ethernet4 | 10.100.0.14/31 |

### Loopback Interfaces (BGP EVPN Peering)

| Loopback Pool | Available Addresses | Assigned addresses | Assigned Address % |
| ------------- | ------------------- | ------------------ | ------------------ |
| 10.100.1.0/24 | 256 | 6 | 2.35 % |
| 10.100.1.0/25 | 128 | 6 | 4.69 % |
| 10.100.254.0/25 | 128 | 2 | 1.57 % |

### Loopback0 Interfaces Node Allocation

| POD | Node | Loopback0 |
| --- | ---- | --------- |
| DC1 | border1-dc1 | 10.100.1.3/32 |
| DC1 | border2-dc1 | 10.100.1.4/32 |
| DC1 | leaf1-dc1 | 10.100.1.5/32 |
| DC1 | leaf2-dc1 | 10.100.1.6/32 |
| DC1 | spine1-dc1 | 10.100.1.1/32 |
| DC1 | spine2-dc1 | 10.100.1.2/32 |
| DC1 | wan1-dc1 | 10.100.254.1/32 |
| DC1 | wan2-dc1 | 10.100.254.2/32 |

### VTEP Loopback VXLAN Tunnel Source Interfaces (VTEPs Only)

| VTEP Loopback Pool | Available Addresses | Assigned addresses | Assigned Address % |
| --------------------- | ------------------- | ------------------ | ------------------ |
| 10.100.2.0/25 | 128 | 4 | 3.13 % |
| 10.100.255.0/25 | 128 | 0 | 0.0 % |

### VTEP Loopback Node allocation

| POD | Node | Loopback1 |
| --- | ---- | --------- |
| DC1 | border1-dc1 | 10.100.2.3/32 |
| DC1 | border2-dc1 | 10.100.2.3/32 |
| DC1 | leaf1-dc1 | 10.100.2.5/32 |
| DC1 | leaf2-dc1 | 10.100.2.5/32 |
