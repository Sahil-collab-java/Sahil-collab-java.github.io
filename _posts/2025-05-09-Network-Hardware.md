---
title: Network Hardware
date: 2025-05-09 
categories: [Computer Network, Introduction to Networking]
tags: [topology,switch,hubs,router,pan,lan,can,man,wan]
---
# 🌐 Network Hardware

## 📌 Introduction

### Basic Definition:

- Even if two computers are connected via Wi-Fi ad hoc mode, technically that’s still a network. But typically, networks are more complex.

### Every device on a network is called a "node".

- Every node **must** have a network address — without it, communication isn’t possible.

### LAN Example (Ref: Figure 1):

- A star topology setup with:
    - 5 Computers (Windows, macOS, Ubuntu, etc.)
    - 1 Central Switch
    - 1 Network Printer (connected directly to the switch)
    - 1 Local Printer (connected directly to a specific computer)
    - 1 Scanner
    - 1 Windows Server

![image.png](assets/images/2025-05-09-Network-Hardware/image.png)

### Printer Types:

- 🖨 **Network Printer** → Connected directly to the switch; accessible by all devices on the network.
- 🖨 **Local Printer** → Connected to a single computer; not directly accessible by others on the network.

### 💡 Handy Tips:

- **Star topology**: All devices are connected to a central switch.
- **Local printer** = Like homemade tea. Only the one who made it can enjoy.
- **Network printer** = Like an office coffee machine. Everyone can use it 😎

---

## 🧠 LANs and Network Hardware

### 💡 Networking Analogy:

- Applications/data = traffic or payload on the network.
- OS = traffic controller.
- Hardware & cabling = road system on which data travels.
- Physical topology = layout of hardware + cables.

### 🌐 LAN (Local Area Network):

- Covers a small physical location like an office or building.
- Each node can communicate directly with others.
- Example (Figure 1-7): 5 computers, a network printer, a local printer, a scanner, and a switch — all connected via wired connections.

### 🔁 Switch:

- Receives data on one port and forwards it to the correct port.
- Efficient: sends data only to the target device, unlike old hubs which broadcasted to all.
- Central switch-based layout = star topology.

![image.png](assets/images/2025-05-09-Network-Hardware/image1.png)

### 🧱 Hubs (Old Tech):

- Broadcasted data to all devices.
- Replaced by switches, which are more efficient and reduce traffic.
- The term "hub" is still used generically for central devices.

### 🔌 Network Ports & NICs:

- **Network port** = where the cable plugs in.
- Types:
    - Onboard port → built into the motherboard (Figure 1-9).
    - Modular NIC → plugs into an expansion slot (Figure 1-10).
- Both types are generally referred to as NICs (Network Interface Cards).

![image.png](assets/images/2025-05-09-Network-Hardware/image2.png)

![image.png](assets/images/2025-05-09-Network-Hardware/image3.png)

### 🕸 Topologies:

1. **Star Topology:**
    - All devices connected to a central switch.
    - Most common layout in LANs.
2. **Mesh Topology (Ref: Figure 1-1):**
    - Each device is directly linked to every other device.
    - Highly redundant, but complex.
3. **Bus Topology:**
    - Devices are connected in a line (like daisy-chained switches).
    - Backbone = main high-speed data path linking segments.
4. **Hybrid Topology (Ref: Figure 1-11):**
    - Combination of topologies (e.g., switches in a star, but those switches linked in a bus).
    - Real-world networks are often hybrid.

![image.png](assets/images/2025-05-09-Network-Hardware/image4.png)

1. **Hub-and-Spoke Topology (Ref: Figure 1-12):**
    - Central switch = hub.
    - Peripheral switches = spokes.
    - Each spoke has its own computers → overall structure is a centralized but distributed network.

![image.png](assets/images/2025-05-09-Network-Hardware/image5.png)

---

## 🧠 Routers and LANs

### 🌍 Main Role of a Router:

- Acts as a bridge/gatekeeper between different networks.
- Routes traffic from one network to another (e.g., LAN to Internet).
- Chooses the best path for data transmission.

### 🏠 SOHO Networks (Small Office/Home Office):

- Typically ≤10 devices.
- Use all-in-one consumer-grade router:
    - Combines router + switch + Wi-Fi access point.
    - Has multiple LAN ports + 1 WAN port (for ISP connection).

🔎 Note (Figure 1-10):

- Combo devices are integrated solutions.
- Don't confuse them with enterprise routers, where each port may link to a different LAN.

![image.png](assets/images/2025-05-09-Network-Hardware/image6.png)

### 🔁 Switch vs Router:

| Feature | Switch | Router |
| --- | --- | --- |
| Function | Connects devices within one LAN | Connects multiple LANs/networks |
| Number of LANs | Just one | Multiple |
| Traffic handling | Based on MAC addresses (local) | Based on IP addresses (network-level) |
| Acts as a gateway? | No | Yes |

### 🧠 Memory Aid:

“**Switch manages internal communication, router connects to the outside world.**”

1. 🏢 Enterprise Router Example (Figure 1-14):
    - A single router connects to 3 different LANs.
    - Each LAN has a separate IP/interface.
    - The router is a **multi-homed** device (has multiple IP addresses).

![image.png](assets/images/2025-05-09-Network-Hardware/image7.png)

1. 🚪 Gateway Role:
    - The router serves as a **gateway** between networks.
    - Without a router, devices on different LANs can’t communicate.

### 🧑‍💻 Node vs Host:

| Term | Definition | Example |
| --- | --- | --- |
| Node | Any network-connected device | Computer, router, switch |
| Host | A node that provides or accesses resources | Computer, printer, server |
| End Device | Cisco term for hosts | Smartphone, laptop, printer |
| Intermediary Device | Cisco term for networking equipment | Switch, router, firewall |

### 💡 Key Difference:

- Every **host** is a **node**, but not every **node** is a **host**.

### 🧵 Quick Recap:

- **Router** connects different networks.
- **Switch** connects devices within one network.
- **Hosts** are end devices that provide or use resources.
- **Nodes** include all devices connected to a network.
- Router = **gateway**, especially for LAN-to-Internet communication.
- Combo device = great for SOHO setups (router + switch + Wi-Fi).

---

## 🧠 Network Types – MANs, WANs, PANs, etc.

### 📌 1. Overview of Network Types:

| Type | Description | Typical Range | Example |
| --- | --- | --- | --- |
| **PAN** (Personal Area Network) | Connects personal devices | A few inches to feet | Smartphone ↔ Laptop |
| **BAN** (Body Area Network) | Wearables & biometric devices | Around the human body | Smartwatch, AR glasses |
| **LAN** (Local Area Network) | High-speed in a small area | Room or building | Office PCs with a switch |
| **CAN** (Campus Area Network) | Multiple LANs in one area | College or corporate campus | Library + Admin LAN |
| **MAN** (Metropolitan Area Network) | Covers a city or metro area | City-scale | Govt buildings across a city |
| **WAN** (Wide Area Network) | Connects distant networks | National or global | Office in SF ↔ Office in London |
| **SAN** (Storage Area Network) | Dedicated network for data storage | Data center | Bank storage systems |

---

![image.png](assets/images/2025-05-09-Network-Hardware/image8.png)

### 📌 2. Key Notes:

- ✅ **WAN** covers large distances; **Internet** is the largest WAN.
- ✅ **MAN** and **CAN** can sometimes be used interchangeably.
- ✅ **WAN links** are typically provided by **third-party ISPs**.
- ✅ **PANs** and **BANs** are personal-scale and mostly wireless.
- ✅ **WLAN** = wireless version of LAN (e.g., Wi-Fi at home).

---

### 📌 3. Visual Size Hierarchy (Figure 1-16 idea):

```
markdown
CopyEdit
PAN  <  LAN  <  CAN ≈ MAN  <  WAN
         ↑            ↑
     Home use      City scale

```

- **PAN** ≈ Personal bubble
- **LAN** ≈ Room or building
- **MAN/CAN** ≈ Campus or city
- **WAN** ≈ Country to global

![image.png](assets/images/2025-05-09-Network-Hardware/image9.png)

---

### 📌 4. Key Examples:

- **PAN:** Connecting smartphone to wireless headphones.
- **BAN:** Smartwatch + fitness tracker + AR glasses.
- **LAN:** Office with 5 computers linked via a switch.
- **CAN:** University network with admin, labs, library.
- **MAN:** Government offices in a city connected via fiber.
- **WAN:** Company HQ in Mumbai linked to branch in London.
- **SAN:** Backend data network in a bank’s server room.

---

### 📌 5. Summary Line:

> “The bigger the area a network covers, the bigger its name!”
> 

**PAN → LAN → MAN/CAN → WAN** (SAN & BAN are special-use networks)

---