# WAN

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
| WAN | wan_rr | arista-pf1-ch-test | 10.90.245.41/24 | - | Provisioned | - |
| WAN | wan_rr | arista-pf2-ch-test | 10.90.245.35/24 | - | Provisioned | - |
| WAN | wan_transit | arista-r1-ch-test | 10.90.245.93/24 | - | Provisioned | - |
| WAN | wan_transit | arista-r2-ch-test | 10.90.245.92/24 | - | Provisioned | - |
| WAN | wan_edge | Site-DRF-INET-SDWAN | 10.90.245.94/24 | - | Provisioned | - |
| WAN | wan_edge | Site-Tier1-INET-SDWAN | 10.90.245.42/24 | - | Provisioned | - |
| WAN | wan_edge | Site-Tier3-INET-SDWAN | 10.90.245.55/24 | - | Provisioned | - |

> Provision status is based on Ansible inventory declaration and do not represent real status from CloudVision.

### Fabric Switches with inband Management IP

| POD | Type | Node | Management IP | Inband Interface |
| --- | ---- | ---- | ------------- | ---------------- |

## Fabric Topology

| Type | Node | Node Interface | Peer Type | Peer Node | Peer Interface |
| ---- | ---- | -------------- | --------- | ----------| -------------- |

## Fabric IP Allocation

### Fabric Point-To-Point Links

| Uplink IPv4 Pool | Available Addresses | Assigned addresses | Assigned Address % |
| ---------------- | ------------------- | ------------------ | ------------------ |

### Point-To-Point Links Node Allocation

| Node | Node Interface | Node IP Address | Peer Node | Peer Interface | Peer IP Address |
| ---- | -------------- | --------------- | --------- | -------------- | --------------- |

### Loopback Interfaces (BGP EVPN Peering)

| Loopback Pool | Available Addresses | Assigned addresses | Assigned Address % |
| ------------- | ------------------- | ------------------ | ------------------ |
| 10.254.99.0/30 | 4 | 2 | 50.0 % |
| 10.254.100.1/32 | 1 | 1 | 100.0 % |
| 10.254.108.1/32 | 1 | 1 | 100.0 % |
| 10.254.109.1/32 | 1 | 1 | 100.0 % |
| 10.254.110.1/32 | 1 | 1 | 100.0 % |
| 10.254.120.1/32 | 1 | 1 | 100.0 % |

### Loopback0 Interfaces Node Allocation

| POD | Node | Loopback0 |
| --- | ---- | --------- |
| WAN | arista-pf1-ch-test | 10.254.99.1/32 |
| WAN | arista-pf2-ch-test | 10.254.99.2/32 |
| WAN | arista-r1-ch-test | 10.254.108.1/32 |
| WAN | arista-r2-ch-test | 10.254.109.1/32 |
| WAN | Site-DRF-INET-SDWAN | 10.254.110.1/32 |
| WAN | Site-Tier1-INET-SDWAN | 10.254.100.1/32 |
| WAN | Site-Tier3-INET-SDWAN | 10.254.120.1/32 |

### VTEP Loopback VXLAN Tunnel Source Interfaces (VTEPs Only)

| VTEP Loopback Pool | Available Addresses | Assigned addresses | Assigned Address % |
| --------------------- | ------------------- | ------------------ | ------------------ |
| 192.168.99.0/30 | 4 | 0 | 0.0 % |
| 192.168.100.1/32 | 1 | 0 | 0.0 % |
| 192.168.108.1/32 | 1 | 0 | 0.0 % |
| 192.168.109.1/32 | 1 | 0 | 0.0 % |
| 192.168.110.1/32 | 1 | 0 | 0.0 % |
| 192.168.120.1/32 | 1 | 0 | 0.0 % |

### VTEP Loopback Node allocation

| POD | Node | Loopback1 |
| --- | ---- | --------- |
