# MPLS L3VPN – Multi-Customer ISP Network

### VRF | MPLS | IS-IS | LDP | MP-BGP | OSPF | GNS3

## 📖 Project Overview

This project demonstrates the design and implementation of a service provider network using **MPLS Layer 3 VPN (L3VPN)** to interconnect multiple customer sites securely.

The network simulates an Internet Service Provider (ISP) infrastructure that supports multiple customers while maintaining complete logical separation between their routing environments using **Virtual Routing and Forwarding (VRF)**.

The laboratory was designed and implemented using **GNS3** and Cisco routers.

## 🎯 Project Objectives

- Build a simulated ISP backbone network.
- Isolate multiple customers using VRF.
- Configure IS-IS as the backbone Interior Gateway Protocol (IGP).
- Enable MPLS label switching and LDP.
- Implement MP-BGP to exchange VPNv4 routes between Provider Edge routers.
- Configure OSPF for routing between Customer Edge (CE) and Provider Edge (PE) routers.
- Verify end-to-end connectivity between customer sites.

## 🏗️ Network Topology

The network consists of five routers:

```text
CE-CASA ---- PE-CASA ---- P-Provider ---- PE-BM ---- CE-BM
               |                            |
               +------- MPLS Backbone ------+
```

| Router | Role | Loopback |
|---|---|---|
| CE-CASA | Customer Edge | — |
| PE-CASA | Provider Edge | 1.1.1.1 |
| P-Provider | Core Provider | 2.2.2.2 |
| PE-BM | Provider Edge | 3.3.3.3 |
| CE-BM | Customer Edge | — |

**Provider Autonomous System: AS 65000**

## 🛠️ Technologies Used

| Technology | Purpose |
|---|---|
| VRF | Customer routing isolation |
| MPLS | Label-based packet forwarding |
| IS-IS | Provider backbone routing |
| LDP | MPLS label distribution |
| MP-BGP | VPNv4 route exchange |
| OSPF | CE-PE routing |
| GNS3 | Network simulation |

## 🔐 Virtual Routing and Forwarding (VRF)

The ISP supports two independent customer VPNs:

- `CIH_BANK`
- `BMCE_BANK`

Each VRF maintains its own routing table, allowing customers to use independent IP addressing spaces without exchanging routes unintentionally.

## 🌐 MPLS Backbone

MPLS is enabled between PE-CASA, P-Provider, and PE-BM.

**IS-IS** provides IP connectivity between provider routers, while **LDP** distributes labels required for MPLS forwarding.

## 🔄 MP-BGP VPNv4

Multi-Protocol BGP exchanges customer VPN routes between the two PE routers.

- PE-CASA: `1.1.1.1`
- PE-BM: `3.3.3.3`
- Autonomous System: `65000`
- Address Family: `VPNv4`

Route Distinguishers (RD) identify VPN routes, while Route Targets (RT) control their import and export between VRFs.

## ✅ Network Verification

The implementation can be verified using Cisco IOS commands:

```bash
show isis neighbors
show mpls ldp neighbor
show mpls forwarding-table
show bgp vpnv4 unicast all summary
show ip route vrf CIH_BANK
show ip route vrf BMCE_BANK
ping vrf CIH_BANK <destination-ip>
```

These commands help validate routing adjacencies, MPLS label distribution, VPNv4 BGP sessions, VRF routes, and customer connectivity.

## 📁 Repository Structure

```text
MPLS-L3VPN-VRF-ISIS-MP-BGP/
├── README.md
├── configs/
│   ├── CE-CASA.cfg
│   ├── PE-CASA.cfg
│   ├── P-Provider.cfg
│   ├── PE-BM.cfg
│   └── CE-BM.cfg
├── topology/
├── screenshots/
├── documentation/
└── gns3-project/
```

Future Improvements

- Implement MPLS Traffic Engineering.
- Add network redundancy and failover testing.
- Explore Fast Reroute (FRR).
- Automate router configuration with Python and Ansible.

Author

Khalid HARCHAOUI

Computer Networking & Digital Infrastructure Student

**Areas of Interest:** Network Engineering, Cisco Technologies, MPLS, Routing & Switching, ISP Infrastructure.

---

If you find this project useful, consider starring the repository.
