# Lab 3: DHCP Server Configuration
---

## Overview

In this lab, I configured a **Cisco router as a DHCP server** to automatically assign
IP addresses, subnet masks, default gateways, and DNS server addresses to connected
 PCs. This eliminates the need for manual (static) IP configuration on every
device.

---

## Objectives

- Configure a router interface with a static IP to act as the network gateway
- Set up a DHCP address pool with excluded addresses
- Enable DHCP client mode on all PCs and verify automatic IP assignment
- Verify DHCP leases using `show ip dhcp binding`
- Test end-to-end connectivity using `ping`

---

## Tools & Technologies

| Tool | Purpose |
|---|---|
| Cisco Packet Tracer | Network simulation environment |
| Cisco 2811 Router | Acts as DHCP server and default gateway |
| Cisco 2960 Switch | Layer 2 connectivity for all devices |
| Windows PCs (x3) | DHCP clients |

---

## Network Topology

<img width="1176" height="1032" alt="image" src="https://github.com/user-attachments/assets/10fc1889-23d3-4bed-bf14-df2389fb6c1a" />

| Device | Role | IP Address |
|---|---|---|
| Router (FA0/0) | DHCP Server / Default Gateway | 192.168.1.1 (static) |
| Switch | Layer 2 Hub | N/A |
| PC0 | DHCP Client | 192.168.1.11 (auto-assigned) |
| PC1 | DHCP Client | 192.168.1.12 (auto-assigned) |
| PC2 | DHCP Client | 192.168.1.13 (auto-assigned) |

> **Note:** Addresses 192.168.1.1–192.168.1.10 are excluded from the DHCP pool
> and reserved for static infrastructure devices (routers, servers, printers).

---

## Configuration Steps

### Step 1 — Configure the Router Interface

Assigned the router a static IP on the LAN-facing interface and brought it online:

```
enable
configure terminal
interface FastEthernet0/0
ip address 192.168.1.1 255.255.255.0
no shutdown
exit
```

<img width="1574" height="484" alt="image" src="https://github.com/user-attachments/assets/b9270260-12c0-4dbd-a1c1-515580781cc6" />

> **Why:** Router interfaces are administratively shut down by default.
> `no shutdown` activates the interface so it can communicate with the switch.

---

### Step 2 — Configure the DHCP Pool

Excluded static-reserved addresses, then created the DHCP pool:

```
ip dhcp excluded-address 192.168.1.1 192.168.1.10

ip dhcp pool OFFICE_LAN
network 192.168.1.0 255.255.255.0
default-router 192.168.1.1
dns-server 8.8.8.8
exit
```

<img width="1200" height="202" alt="image" src="https://github.com/user-attachments/assets/b223858c-3061-4f06-ab20-3232c01cd980" />


> **Why:** The `excluded-address` command prevents the DHCP server from handing out
> IPs already assigned to static devices, avoiding IP conflicts. The pool defines
> the full subnet scope and tells clients where to find their gateway and DNS server.

---

### Step 3 — Enable DHCP on Client PCs

On each PC: **Desktop tab → IP Configuration → DHCP**

Each PC automatically received:
- IP Address (starting at 192.168.1.11)
- Subnet Mask: 255.255.255.0
- Default Gateway: 192.168.1.1
- DNS Server: 8.8.8.8

<img width="2298" height="678" alt="image" src="https://github.com/user-attachments/assets/ca5c71fc-44d8-4f3f-9e04-9a0039ed5ba7" />

<img width="500" height="670" alt="image" src="https://github.com/user-attachments/assets/229f4c98-844f-4668-8f7a-ceb955e86f18" /><img width="500" height="718" alt="image" src="https://github.com/user-attachments/assets/cac19e7d-c699-4a6c-9cc1-c00087a98229" />


---

##  Verification

### DHCP Binding Table

Verified active leases from the router CLI:

```
show ip dhcp binding
```

<img width="1508" height="648" alt="image" src="https://github.com/user-attachments/assets/3cccf49b-c2b5-4e0f-abe7-c07025b54f41" />


This confirms the router successfully leased unique IPs to all three PCs and
recorded their MAC addresses — the same table a network admin would check in a
real environment to trace which device holds which IP.

---

### Connectivity Test

Ran a ping from PC0 to the default gateway to confirm end-to-end connectivity:

```
ping 192.168.1.1
```

<img width="1280" height="780" alt="image" src="https://github.com/user-attachments/assets/05e4de66-6443-4351-be83-f9b57e3bd3e5" />


All 4 packets succeeded, confirming the PC can communicate with the router.

---

## Key Concepts Learned

### The DHCP DORA Process
When a PC requests an IP, a 4-step handshake occurs:

| Step | Message | Description |
|---|---|---|
| 1 | **Discover** | PC broadcasts "I need an IP address" |
| 2 | **Offer** | Router replies with an available IP offer |
| 3 | **Request** | PC formally requests the offered IP |
| 4 | **Acknowledge** | Router confirms the lease |

### Excluded Addresses
Real networks always reserve a block of IPs for **static devices** — routers,
servers, printers, and access points. Without exclusions, a DHCP client could
accidentally receive an IP already in use, causing a conflict.

