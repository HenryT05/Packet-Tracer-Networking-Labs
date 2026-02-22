# Lab 2: Simple Routed Network (Two Subnets)

## Objective
Configure a router to enable communication between two PCs on separate subnets.
This lab introduces Layer 3 routing — the idea that a router is required to forward
traffic between different IP networks. A switch alone cannot do this.

---

## Network Topology

```
PC1 ──── Switch1 ──── Router ──── Switch2 ──── PC2
        192.168.1.0/24            192.168.2.0/24
```

| Device | Interface  | IP Address   | Subnet Mask   | Default Gateway |
|--------|------------|--------------|---------------|-----------------|
| PC1    | NIC        | 192.168.1.10 | 255.255.255.0 | 192.168.1.1     |
| Router | Gig0/0     | 192.168.1.1  | 255.255.255.0 | —               |
| Router | Gig0/1     | 192.168.2.1  | 255.255.255.0 | —               |
| PC2    | NIC        | 192.168.2.10 | 255.255.255.0 | 192.168.2.1     |

---

## Step-by-Step Configuration

### Step 1 — Build the Topology
Placed two PCs, two switches, and one router on the workspace. Connected devices
using Copper Straight-Through cables:
- PC1 → Switch1 → Router (Gig0/0)
- Router (Gig0/1) → Switch2 → PC2

Straight-through cables are used when connecting different types of devices
(PC to switch, switch to router).

<img width="1240" height="242" alt="image" src="https://github.com/user-attachments/assets/5d50e87d-0984-48bd-9823-a59ee4ed2b12" />


---

### Step 2 — Configure Router Interface Gig0/0 (Subnet 1)
Opened the Router CLI and entered the following commands:

```
Router> enable
Router# configure terminal
Router(config)# interface gigabitethernet 0/0
Router(config-if)# ip address 192.168.1.1 255.255.255.0
Router(config-if)# no shutdown
Router(config-if)# exit
```

`no shutdown` is required because Cisco router interfaces are administratively
down by default — a built-in security feature.

<img width="796" height="390" alt="image" src="https://github.com/user-attachments/assets/78cfcb93-9c8b-484b-acac-d672fbb89c4a" />


---

### Step 3 — Configure Router Interface Gig0/1 (Subnet 2)

```
Router(config)# interface gigabitethernet 0/1
Router(config-if)# ip address 192.168.2.1 255.255.255.0
Router(config-if)# no shutdown
Router(config-if)# exit
Router(config)# exit
Router# write memory
```

`write memory` saves the config so it persists after a reload.

<img width="823" height="405" alt="image" src="https://github.com/user-attachments/assets/898f90fd-1ad8-4e5c-9a92-344851a849fa" />


---

### Step 4 — Verify Router Interfaces
Ran `show ip interface brief` to confirm both interfaces are up with correct IPs.

```
Router# show ip interface brief
```

---

### Step 5 — Configure PC1
Opened PC1 → Desktop → IP Configuration and set:
- IP Address: `192.168.1.10`
- Subnet Mask: `255.255.255.0`
- Default Gateway: `192.168.1.1`

The default gateway tells PC1 where to send traffic destined outside its subnet.
Without it, cross-subnet communication fails entirely.

<img width="1094" height="183" alt="image" src="https://github.com/user-attachments/assets/41b6944d-e737-4da1-92d3-d435088bd3df" />

---

### Step 6 — Configure PC2
Opened PC2 → Desktop → IP Configuration and set:
- IP Address: `192.168.2.10`
- Subnet Mask: `255.255.255.0`
- Default Gateway: `192.168.2.1`

<img width="1084" height="192" alt="image" src="https://github.com/user-attachments/assets/4907b4bd-dd84-40b3-802c-bb5f3f58d746" />

---

### Step 7 — Test Connectivity with Ping
Opened PC1 Command Prompt and ran:

```
ping 192.168.2.10
```

Then verified the reverse from PC2:

```
ping 192.168.1.10
```

The first ping may show one timeout — this is normal. ARP (Address Resolution
Protocol) needs one round-trip to resolve the MAC address of the default gateway
before traffic can flow.

<img width="524" height="265" alt="image" src="https://github.com/user-attachments/assets/e833b164-3349-4ae4-b3b0-622cde290ad6" />


---

### Step 8 — View the Routing Table
Ran `show ip route` on the router to see how it knows where to send packets.

```
Router# show ip route
```

Two `C` (Connected) routes appear — one per subnet. This is the router's internal
map it uses to make forwarding decisions.

<img width="723" height="314" alt="image" src="https://github.com/user-attachments/assets/5ddc5bbc-6bc7-45ff-adfd-dde5d25cae1d" />


---

## Key Concepts Learned

- **Layer 2 vs Layer 3**: Switches forward by MAC address (same subnet only);
  routers forward by IP address across different subnets
- **Default Gateway**: The router IP on the local subnet — required on every end device
- **`no shutdown`**: Router interfaces are off by default and must be manually enabled
- **ARP**: Resolves IP addresses to MAC addresses before communication can begin
- **Routing Table**: The router's internal map — viewable with `show ip route`

---

## Skills Demonstrated
`Packet Tracer` `Layer 3 Routing` `IP Addressing` `Subnetting` `Cisco IOS CLI`
`Default Gateway` `show ip interface brief` `show ip route`
