---
title: Network Models
date: 2025-05-07 
categories: [Computer Network, Introduction to Networking]
tags: [peer-to-peer , client-server]
---

# 1: Network Models

### **Definition: P2P (Peer-to-Peer) Network**

- In a **P2P (Peer-to-Peer)** network, **each computer manages its own resources and security** — there is **no central server**.
- All computers (also called nodes or hosts) form a **logical group** to share resources such as files, printers, etc.

### 🔹 **Key Features**

- ❌ **No centralized control** – all nodes operate independently.
- 💻 **Nodes/Hosts** = Computers or devices in the network that share data.
- 🔁 **Resource Sharing** = Sharing files, printers, internet, and more.

### 🔹 **Supported Devices/Operating Systems**

- **Desktops/Laptops:** Windows, Linux, macOS, ChromeOS
- **Mobile/Tablets:** Android, iOS, ChromeOS

### 🔹 **Diagram Reference**

![image.png](images/2025-05-07-Network Models/image.png)

- The diagram represents a **Mesh Topology**: each device is connected to multiple others, indicated by lines showing direct connections.

### 🔹 **Author’s Insight**

- P2P is a **decentralized** model.
- Best suited for **small or simple networks**.
- Every computer is a **peer**, with equal responsibilities.

---

### 🧠 **Summary **

> In a P2P network, every computer acts independently—there’s no central authority. Each device manages its own security and resources. Figure 1-1  illustrates its mesh-style topology. It’s ideal for simple, decentralized setups.
> 

---

### **Definition: Client–Server Network**

- The **Client–Server** model is a **centralized network** where a **server** controls resources and user access.
- The server runs a **Network Operating System (NOS)** and manages the network via a **central database** (e.g., Active Directory).

### 🔹 **Core Components**

- **NOS Examples:** Windows Server, Ubuntu Server, Red Hat Enterprise Linux
- **Database:** Stores information like users, passwords, and shared resources.
    
    In Microsoft networks, a common example is **Active Directory (AD)**.
    

### 🔹 **Client & Server Roles**

- **Server:** Provides services and manages the central database.
- **Client:** Sends requests to the server (e.g., for files, printers).
- **Clients do not directly access each other** — all communication goes through the server.

### 🔹 **Access Control**

- Centralized control using **Active Directory** (in Microsoft environments).
- Users can log in from any connected device and access permitted resources.

### 🔹 **Diagram Reference**

![image1.png](images/2025-05-07-Network Models/image1.png)

- The diagram shows a **Windows Server domain controller** with connected clients (Windows, macOS, Ubuntu, etc.).
- Physically, the network often uses a **Star Topology** (with the server as the central hub).

### 🔹 **Advantages**

- ✅ Centralized user and resource management
- ✅ Controlled and secure resource sharing
- ✅ Superior **security**, **scalability**, and **efficiency** compared to P2P

---

### 🧠 **Summary **

> The Client–Server model is a centralized network setup where the server manages everything using a NOS and a central database (like Active Directory). Clients send requests but the server maintains control. This model is more powerful than P2P and ideal for larger networks. Figure 1-2 (Pg 5) illustrates this structure.
>
