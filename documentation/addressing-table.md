# IP Addressing Table

## Provider Backbone

| Router | Interface | IP Address | Purpose |
|---|---|---|---|
| PE-CASA | Loopback0 | 1.1.1.1/32 | Router ID / LDP / MP-BGP |
| PE-CASA | FastEthernet0/0 | 10.0.0.2/30 | Link to P |
| P | Loopback0 | 2.2.2.2/32 | Router ID / LDP |
| P | FastEthernet0/0 | 10.0.0.1/30 | Link to PE-CASA |
| P | FastEthernet1/0 | 10.0.1.1/30 | Link to PE-BM |
| PE-BM | Loopback0 | 3.3.3.3/32 | Router ID / LDP / MP-BGP |
| PE-BM | FastEthernet0/0 | 10.0.1.2/30 | Link to P |

---

## CIH Customer

### Casablanca

| Device | Interface | IP Address | Purpose |
|---|---|---|---|
| PE-CASA | FastEthernet1/0 | 172.16.10.1/30 | PE-CE link |
| CE-CIH-CASA | FastEthernet0/0 | 172.16.10.2/30 | PE-CE link |
| CE-CIH-CASA | FastEthernet1/0 | 192.168.10.1/24 | LAN Gateway |
| CIH Client | — | 192.168.10.10/24 | End Host |

### Beni Mellal

| Device | Interface | IP Address | Purpose |
|---|---|---|---|
| PE-BM | FastEthernet1/0 | 172.16.20.1/30 | PE-CE link |
| CE-CIH-BM | FastEthernet0/0 | 172.16.20.2/30 | PE-CE link |
| CE-CIH-BM | FastEthernet1/0 | 192.168.20.1/24 | LAN Gateway |
| CIH Server | — | 192.168.20.254/24 | End Host |

---

## BMCE Customer

### Casablanca

| Device | Interface | IP Address | Purpose |
|---|---|---|---|
| PE-CASA | FastEthernet1/1 | 172.16.30.1/30 | PE-CE link |
| CE-BMCE-CASA | FastEthernet0/0 | 172.16.30.2/30 | PE-CE link |
| CE-BMCE-CASA | FastEthernet1/0 | 192.168.30.1/24 | LAN Gateway |
| BMCE Server | — | 192.168.30.100/24 | End Host |

### Beni Mellal

| Device | Interface | IP Address | Purpose |
|---|---|---|---|
| PE-BM | FastEthernet1/1 | 172.16.40.1/30 | PE-CE link |
| CE-BMCE-BM | FastEthernet0/0 | 172.16.40.2/30 | PE-CE link |
| CE-BMCE-BM | FastEthernet1/0 | 192.168.40.1/24 | LAN Gateway |
| BMCE Client | — | 192.168.40.55/24 | End Host |

---

## VRF Information

| Customer | VRF | RD | Route Target |
|---|---|---|---|
| CIH | CIH | 65000:1 | 65000:1 |
| BMCE | BMCE | 65000:2 | 65000:2 |

---

## Routing Protocols

| Protocol | Usage |
|---|---|
| IS-IS | Provider backbone routing |
| LDP | MPLS label distribution |
| MPLS | Label-based forwarding |
| MP-BGP | VPNv4 route exchange between PE routers |
| OSPF 10 | CIH CE-PE routing |
| OSPF 20 | BMCE CE-PE routing |

---

## Autonomous System

```text
Provider AS: 65000
