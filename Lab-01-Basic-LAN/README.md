# Lab 01 – Basic LAN with Two PCs and a Switch

## Objective

Set up a basic Local Area Network (LAN) by connecting two PCs to a Layer 2 switch, 
assigning static IP addresses on the same subnet, and verifying connectivity using 
ping. This lab demonstrates how Layer 2 switching and ARP (Address Resolution 
Protocol) work together to enable communication between devices on the same network.

---

## Tools & Environment

| Tool | Details |
|------|---------|
| Cisco Packet Tracer | Version 8.x |
| Devices Used | 2x Generic PC, 1x Cisco 2960 Switch |
| Cable Type | Copper Straight-Through |
| Platform | Windows / macOS |

---

## IP Addressing Scheme

| Device | Interface      | IP Address   | Subnet Mask   | Default Gateway |
|--------|---------------|--------------|---------------|-----------------|
| PC0    | FastEthernet0 | 192.168.1.1  | 255.255.255.0 | N/A             |
| PC1    | FastEthernet0 | 192.168.1.2  | 255.255.255.0 | N/A             |
| Switch | —             | N/A          | —             | —               |

> No default gateway is needed — both PCs are on the same subnet with no router.

---

## Configuration Steps

### Step 1 – Build the Topology
1. Open Cisco Packet Tracer
2. Add two **Generic PCs** and one **Cisco 2960 Switch** to the workspace
3. Connect using **Copper Straight-Through** cables:
   - PC0 FastEthernet0 → Switch FastEthernet0/1
   - PC1 FastEthernet0 → Switch FastEthernet0/2
4. Wait for link lights to turn **green** (STP convergence ~30 seconds)

<img width="1156" height="627" alt="image" src="https://github.com/user-attachments/assets/373f337e-2ad5-4b41-b0b1-af9bc6c622eb" />


### Step 2 – Assign Static IPs
**PC0:**
- Click PC0 → Desktop → IP Configuration → Static
- IP: `192.168.1.1` | Subnet: `255.255.255.0`

<img width="1041" height="351" alt="image" src="https://github.com/user-attachments/assets/c3af605e-73f3-4228-9f2e-0d45e3a652b1" />

**PC1:**
- Click PC1 → Desktop → IP Configuration → Static
- IP: `192.168.1.2` | Subnet: `255.255.255.0`

### Step 3 – Test Connectivity
Open PC0's Command Prompt (Desktop → Command Prompt) and run:
```
ping 192.168.1.2
```
<img width="306" height="186" alt="image" src="https://github.com/user-attachments/assets/0ba2b415-d90e-49ed-bb9b-6828dd314067" />


### Step 4 – Verify ARP Table
In PC0's Command Prompt:
```
arp -a
```
<img width="400" height="200" alt="image" src="https://github.com/user-attachments/assets/121c8083-be80-46f2-a118-b4ce9ba4e15c" />

This confirms PC0 successfully mapped PC1's IP address to its MAC address.

### Step 5 – Verify Switch MAC Address Table
Click Switch → CLI tab → press Enter, then run:
```
show mac address-table
```
<img width="256" height="105" alt="image" src="https://github.com/user-attachments/assets/8b9dc1eb-a01f-4359-a3eb-846f4857ddb0" />


---

## Concepts Learned

### Layer 2 Switching
A Layer 2 switch forwards Ethernet frames based on **MAC addresses**, not IP addresses. 
When a frame arrives, the switch checks its MAC address table to determine which port 
the destination MAC lives on and forwards the frame only to that port. If it doesn't 
know the destination, it floods the frame to all ports — this is called **unknown 
unicast flooding**.

### ARP (Address Resolution Protocol)
Before PC0 can send an ICMP ping to PC1, it needs PC1's MAC address. Since it only 
knows the IP (`192.168.1.2`), it sends a **broadcast ARP request** to all devices: 
*"Who has 192.168.1.2? Tell 192.168.1.1."* PC1 responds with its MAC address. 
PC0 stores this in its ARP cache — which we verified with `arp -a`.

### Why the First Ping Times Out
The first ICMP echo request is dropped because ARP resolution hasn't happened yet. 
The ARP broadcast goes out, the reply comes back, and then the remaining pings go 
through successfully.

---

## Results

- ✅ Physical topology built with straight-through cables
- ✅ Static IPs assigned on the `192.168.1.0/24` subnet
- ✅ Successful ping from PC0 to PC1 (3/4 replies — first timed out due to ARP)
- ✅ ARP cache populated on PC0 with PC1's MAC address
- ✅ Switch MAC address table confirmed correct port-to-MAC mappings

---

## Files

| File | Description |
|------|-------------|
| `Lab01_Basic_LAN.pkt` | Cisco Packet Tracer source file |

---

## Skills Demonstrated

`Cisco Packet Tracer` `Layer 2 Switching` `ARP` `Static IP Configuration` 
`ICMP / Ping` `MAC Address Tables` `Subnetting` `LAN Fundamentals`
