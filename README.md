# MPLS L3VPN – Multi-Customer ISP Network

### VRF | MPLS | IS-IS | LDP | MP-BGP | OSPF | GNS3

## 📖 Project Overview

This project demonstrates the design and implementation of a **Multi-Protocol Label Switching Layer 3 VPN (MPLS L3VPN)** network for an Internet Service Provider.

The objective is to interconnect multiple customer sites through a shared ISP backbone while keeping each customer's routing environment logically separated.

The network supports two customers:

- **CIH**
- **BMCE**

Each customer has two geographically separated sites:

- Casablanca
- Beni Mellal

The ISP backbone is composed of two **Provider Edge (PE)** routers and one **Provider (P)** core router.

The laboratory was designed and implemented using **GNS3** and Cisco IOS routers.

---

## 🎯 Project Objectives

- Build a simulated ISP backbone.
- Connect multiple customer sites through the same provider infrastructure.
- Isolate customers using **VRF**.
- Use **IS-IS** as the provider Interior Gateway Protocol.
- Enable **MPLS** packet forwarding.
- Use **LDP** to distribute MPLS labels.
- Use **MP-BGP VPNv4** to exchange customer routes between PE routers.
- Use **OSPF** between CE and PE routers.
- Configure route redistribution between OSPF and MP-BGP.
- Verify end-to-end customer connectivity.
- Demonstrate how MPLS L3VPN separates multiple customers while sharing the same backbone.

---

## 🏗️ Network Architecture

```text
                    ISP / MPLS Backbone
                Autonomous System 65000

              PE-CASA ----- P ----- PE-BM
              /     \               /     \
             /       \             /       \
      CE-CIH-CASA  CE-BMCE-CASA  CE-CIH-BM  CE-BMCE-BM
           |             |            |            |
     192.168.10.0   192.168.30.0  192.168.20.0  192.168.40.0
          /24            /24           /24           /24
```

---

## 🖧 Router Roles

| Router | Role | Loopback |
|---|---|---|
| PE-CASA | Provider Edge | 1.1.1.1/32 |
| P-Provider | Provider Core | 2.2.2.2/32 |
| PE-BM | Provider Edge | 3.3.3.3/32 |
| CE-CIH-CASA | Customer Edge | — |
| CE-CIH-BM | Customer Edge | — |
| CE-BMCE-CASA | Customer Edge | — |
| CE-BMCE-BM | Customer Edge | — |

---

## 🌍 Autonomous System

The provider network uses:

```text
AS 65000
```

AS 65000 is a **private Autonomous System Number** used for this laboratory.

The MP-BGP session is established between:

```text
PE-CASA 1.1.1.1
        |
        | MP-iBGP
        |
PE-BM   3.3.3.3
```

Because both PE routers belong to the same AS, the session is an **iBGP session**.

---

## 📡 IP Addressing Plan

### Provider Backbone

| Link | Network |
|---|---|
| PE-CASA ↔ P-Provider | 10.0.0.0/30 |
| P-Provider ↔ PE-BM | 10.0.1.0/30 |

### Provider Interfaces

| Router | Interface | IP Address |
|---|---|---|
| PE-CASA | Loopback0 | 1.1.1.1/32 |
| PE-CASA | Fa0/0 | 10.0.0.2/30 |
| P-Provider | Loopback0 | 2.2.2.2/32 |
| P-Provider | Fa0/0 | 10.0.0.1/30 |
| P-Provider | Fa1/0 | 10.0.1.1/30 |
| PE-BM | Loopback0 | 3.3.3.3/32 |
| PE-BM | Fa0/0 | 10.0.1.2/30 |

---

## 🏦 CIH Customer

### Casablanca Site

| Device | Network / IP |
|---|---|
| LAN | 192.168.10.0/24 |
| CE Gateway | 192.168.10.1 |
| CE ↔ PE Link | 172.16.10.0/30 |
| PE-CASA | 172.16.10.1 |
| CE-CIH-CASA | 172.16.10.2 |

### Beni Mellal Site

| Device | Network / IP |
|---|---|
| LAN | 192.168.20.0/24 |
| CE Gateway | 192.168.20.1 |
| PE ↔ CE Link | 172.16.20.0/30 |
| PE-BM | 172.16.20.1 |
| CE-CIH-BM | 172.16.20.2 |

---

## 🏦 BMCE Customer

### Casablanca Site

| Device | Network / IP |
|---|---|
| LAN | 192.168.30.0/24 |
| CE Gateway | 192.168.30.1 |
| CE ↔ PE Link | 172.16.30.0/30 |
| PE-CASA | 172.16.30.1 |
| CE-BMCE-CASA | 172.16.30.2 |

### Beni Mellal Site

| Device | Network / IP |
|---|---|
| LAN | 192.168.40.0/24 |
| CE Gateway | 192.168.40.1 |
| PE ↔ CE Link | 172.16.40.0/30 |
| PE-BM | 172.16.40.1 |
| CE-BMCE-BM | 172.16.40.2 |

---

## 🔐 Virtual Routing and Forwarding – VRF

Two VRFs are configured on both PE routers:

```text
CIH
BMCE
```

VRF allows the provider to maintain independent routing tables for each customer.

This means that CIH and BMCE can use the same MPLS backbone without exchanging routes with each other.

### CIH VRF

```cisco
ip vrf CIH
 rd 65000:1
 route-target export 65000:1
 route-target import 65000:1
```

### BMCE VRF

```cisco
ip vrf BMCE
 rd 65000:2
 route-target export 65000:2
 route-target import 65000:2
```

---

## 🏷️ Route Distinguisher and Route Target

### Route Distinguisher – RD

The RD makes VPN routes unique.

```text
CIH  → 65000:1
BMCE → 65000:2
```

The RD does not define the BGP Autonomous System.

The BGP Autonomous System is configured using:

```cisco
router bgp 65000
```

### Route Target – RT

Route Targets control which VPN routes are imported and exported by each VRF.

```text
CIH
Export RT: 65000:1
Import RT: 65000:1
```

```text
BMCE
Export RT: 65000:2
Import RT: 65000:2
```

This keeps CIH routes inside the CIH VPN and BMCE routes inside the BMCE VPN.

---

## 🛰️ IS-IS Provider Backbone

**IS-IS (Intermediate System to Intermediate System)** is used as the Interior Gateway Protocol inside the provider backbone.

It provides IP reachability between:

```text
PE-CASA
P-Provider
PE-BM
```

The provider Loopback interfaces are advertised through IS-IS:

```text
PE-CASA    → 1.1.1.1
P-Provider → 2.2.2.2
PE-BM      → 3.3.3.3
```

These Loopback addresses are used by LDP and MP-BGP.

### Example IS-IS Configuration

```cisco
router isis CORE
 net 49.0001.0000.0000.0001.00
 is-type level-2-only
```

Interface activation:

```cisco
interface Loopback0
 ip router isis CORE

interface FastEthernet0/0
 ip router isis CORE
```

---

## 🏷️ MPLS

**MPLS (Multi-Protocol Label Switching)** forwards packets using labels.

The MPLS backbone is:

```text
PE-CASA ---- P-Provider ---- PE-BM
```

MPLS is enabled on provider-facing interfaces.

Example:

```cisco
interface FastEthernet0/0
 mpls ip
```

MPLS is not enabled on customer-facing interfaces.

---

## 🔄 LDP

**LDP = Label Distribution Protocol**

LDP distributes MPLS labels between provider routers.

Example:

```cisco
mpls label protocol ldp
mpls ldp router-id Loopback0 force
```

Verification:

```cisco
show mpls ldp neighbor
```

---

## 📚 MPLS Forwarding Tables

### RIB

**Routing Information Base**

Contains IP routes learned from routing protocols such as IS-IS.

### LIB

**Label Information Base**

Contains MPLS label bindings.

### LFIB

**Label Forwarding Information Base**

Used to forward MPLS packets.

```text
IS-IS
  ↓
RIB
  ↓
LDP
  ↓
LIB
  ↓
LFIB
  ↓
MPLS Forwarding
```

---

## 🔖 MPLS Label Structure

An MPLS label header contains **32 bits**.

```text
+--------------------+------+---+--------+
| Label - 20 bits    | TC 3 | S | TTL 8 |
+--------------------+------+---+--------+
```

| Field | Size | Purpose |
|---|---:|---|
| Label | 20 bits | MPLS forwarding identifier |
| TC | 3 bits | Traffic Class / QoS |
| S | 1 bit | Bottom of Stack |
| TTL | 8 bits | Loop prevention |

---

## 🔄 MPLS Operations

MPLS routers perform three main operations:

```text
PUSH
SWAP
POP
```

### PUSH

The ingress PE adds an MPLS label.

### SWAP

A P router replaces an incoming label with another label.

### POP

The MPLS label is removed when the packet approaches the egress PE.

---

## 🌐 MP-BGP

**MP-BGP = Multiprotocol Border Gateway Protocol**

MP-BGP is an extension of BGP that supports multiple address families.

In this project, MP-BGP exchanges:

```text
VPNv4 routes
```

between:

```text
PE-CASA ↔ PE-BM
```

Both PE routers belong to:

```text
AS 65000
```

Therefore, the MP-BGP session is an **MP-iBGP session**.

### PE-CASA

```cisco
router bgp 65000
 bgp log-neighbor-changes

 neighbor 3.3.3.3 remote-as 65000
 neighbor 3.3.3.3 update-source Loopback0

 address-family vpnv4
  neighbor 3.3.3.3 activate
  neighbor 3.3.3.3 send-community extended
 exit-address-family
```

### PE-BM

```cisco
router bgp 65000
 bgp log-neighbor-changes

 neighbor 1.1.1.1 remote-as 65000
 neighbor 1.1.1.1 update-source Loopback0

 address-family vpnv4
  neighbor 1.1.1.1 activate
  neighbor 1.1.1.1 send-community extended
 exit-address-family
```

---

## 🔁 BGP, eBGP, iBGP and MP-BGP

### BGP

**BGP = Border Gateway Protocol**

BGP is a routing protocol used to exchange routes between Autonomous Systems or between BGP routers inside the same Autonomous System.

### eBGP

**eBGP = External BGP**

Used between routers belonging to different Autonomous Systems.

```text
AS 65000 <---- eBGP ----> AS 65100
```

### iBGP

**iBGP = Internal BGP**

Used between BGP routers belonging to the same Autonomous System.

```text
PE-CASA AS65000 <---- iBGP ----> PE-BM AS65000
```

### MP-BGP

**MP-BGP = Multiprotocol BGP**

MP-BGP supports multiple address families such as:

```text
IPv4
IPv6
VPNv4
VPNv6
```

In this project, MP-BGP exchanges **VPNv4 routes**.

---

## 🔢 Autonomous System

An **Autonomous System (AS)** is a group of routers and networks managed under the same administrative routing policy.

Each Autonomous System is identified by an **Autonomous System Number (ASN)**.

This project uses:

```text
AS 65000
```

AS 65000 is a private ASN used to represent the ISP in the laboratory.

---

## 🛣️ OSPF CE-PE Routing

OSPF is used between Customer Edge and Provider Edge routers.

Two OSPF processes are used:

```text
OSPF 10 → CIH
OSPF 20 → BMCE
```

### CIH Example

```cisco
router ospf 10
 router-id 10.10.10.1
 network 172.16.10.0 0.0.0.3 area 0
 network 192.168.10.0 0.0.0.255 area 0
```

On the PE:

```cisco
router ospf 10 vrf CIH
 network 172.16.10.0 0.0.0.3 area 0
```

### BMCE Example

```cisco
router ospf 20
 router-id 20.20.30.1
 network 172.16.30.0 0.0.0.3 area 0
 network 192.168.30.0 0.0.0.255 area 0
```

On the PE:

```cisco
router ospf 20 vrf BMCE
 network 172.16.30.0 0.0.0.3 area 0
```

---

## 🔄 Route Redistribution

Routes learned through OSPF are redistributed into BGP.

```cisco
router bgp 65000

 address-family ipv4 vrf CIH
  redistribute ospf 10
 exit-address-family

 address-family ipv4 vrf BMCE
  redistribute ospf 20
 exit-address-family
```

BGP routes are also redistributed back into OSPF:

```cisco
router ospf 10 vrf CIH
 redistribute bgp 65000 subnets

router ospf 20 vrf BMCE
 redistribute bgp 65000 subnets
```

This allows the remote customer routes learned through MP-BGP to be advertised toward the CE routers.

---

## 🔗 Protocol Relationships

```text
OSPF
  ↓
Customer routes reach the PE
  ↓
VRF
  ↓
Customer routes are separated
  ↓
MP-BGP
  ↓
VPNv4 routes are exchanged between PEs

IS-IS
  ↓
Provides backbone IP reachability
  ↓
LDP
  ↓
Distributes MPLS labels
  ↓
MPLS
  ↓
Transports traffic across the provider backbone
```

### Simple Summary

| Technology | Role |
|---|---|
| VRF | Customer isolation |
| IS-IS | Provider backbone routing |
| LDP | MPLS label distribution |
| MPLS | Label-based packet forwarding |
| MP-BGP | VPNv4 route exchange |
| OSPF | CE-PE routing |

---

## ✅ Verification Commands

### IS-IS

```cisco
show isis neighbors
show ip route isis
```

### MPLS / LDP

```cisco
show mpls ldp neighbor
show mpls forwarding-table
show mpls interfaces
```

### MP-BGP

```cisco
show ip bgp vpnv4 all summary
show ip bgp vpnv4 all
```

Depending on the IOS version:

```cisco
show bgp vpnv4 unicast all summary
```

### VRF

```cisco
show ip vrf
show ip route vrf CIH
show ip route vrf BMCE
```

### OSPF

```cisco
show ip ospf neighbor
show ip route ospf
```

---

## 🧪 Connectivity Tests

### CIH

```text
192.168.10.10
      ↓
192.168.20.254
```

Example:

```text
ping 192.168.20.254
```

### BMCE

```text
192.168.30.100
      ↓
192.168.40.55
```

The CIH and BMCE networks remain isolated because they use separate VRFs.

---

## 📁 Repository Structure

```text
MPLS-L3VPN-Multi-Customer-ISP-Network/
│
├── README.md
│
├── configs/
│   ├── PE-CASA.cfg
│   ├── P-Provider.cfg
│   ├── PE-BM.cfg
│   ├── CE-CIH-CASA.cfg
│   ├── CE-CIH-BM.cfg
│   ├── CE-BMCE-CASA.cfg
│   └── CE-BMCE-BM.cfg
│
├── topology/
│   └── network-topology.png
│
├── screenshots/
│   ├── isis-neighbors.png
│   ├── ldp-neighbors.png
│   ├── mpls-forwarding-table.png
│   ├── mp-bgp-vpnv4.png
│   ├── vrf-routing-tables.png
│   └── connectivity-tests.png
│
├── documentation/
│   ├── addressing-table.md
│   └── MPLS-L3VPN-Presentation.pdf
│
└── gns3-project/
```

---

## 🚀 Future Improvements

- Add redundant PE and P routers.
- Implement MPLS Traffic Engineering.
- Implement Fast Reroute (FRR).
- Add QoS.
- Add IPv6 and VPNv6.
- Test eBGP between CE and PE.
- Implement BGP Route Reflectors.
- Add network monitoring.
- Automate configurations using Python.
- Automate deployments using Ansible.
- Perform link-failure and convergence tests.

---

## 🛠️ Tools

- GNS3
- Cisco IOS
- Wireshark
- Git
- GitHub

---

## 📚 Technologies

- MPLS
- MPLS L3VPN
- VRF
- IS-IS
- LDP
- BGP
- iBGP
- MP-BGP
- VPNv4
- OSPF
- Route Distinguisher
- Route Target

---

## 👨‍💻 Author

**Khalid HARCHAOUI**

Computer Networking & Digital Infrastructure Student

### Areas of Interest

- Network Engineering
- Cisco Technologies
- Routing & Switching
- MPLS
- ISP Infrastructure
- Network Automation
- Cybersecurity

---

## ⭐ Project Purpose

This laboratory was created for educational purposes to demonstrate how service providers can use **MPLS L3VPN** to connect multiple customers over a shared provider infrastructure while maintaining complete logical routing separation between customers.
