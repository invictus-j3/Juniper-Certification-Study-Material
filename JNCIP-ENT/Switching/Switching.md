# Advanced Enterprise Switching

## VLAN Traffic Management

| Port Mode      | Ether-type |
| -------------- | ---------- |
| Access         | 0x0800     |
| Trunk          | 0x8100     |
| S-VLAN (Q-in-Q)| 0x88A8     |
| C-VLAN (Q-in-Q)| 0x8100     |

## Private VLAN

Splits a broadcast domain into multiple isolated broadcast subdomains.

Consists of a primary VLAN and nested secondary VLANs

Secondary VLAN types are __community__ and __isolated__. Many community VLANs can exists that can send traffic between hosts on the same community VLAN and primary VLAN. Isolated VLANs can ONLY receive traffic from primary VLAN. Only one isolated VLAN can exist.

### Private VLAN Port Types

| Port Type | Designations|
| --- | --- |
| Promiscuous | Trunk ports typically connect to a router, Layer 2 connectivity to all other switch ports |
| Community | Ports that belong to a community |
| Isolated | Add info here |
| PVLAN Trunk | Connect two switches participating in the same PVLAN, communicate with all ports except isolated |

Promiscuous and trunk ports must include __inter-switch-link__ configuration on both sides. Inter switch links are configured with only the Primary VLAN.

## Multiple VLAN Registration Protocol (MVRP)

Can be used to dynamically create and prune VLANs over trunk links. Standard version of Cisco's VTP. Defined in 802.1ak.

Uses PDUs, which contain MRP messages, to communicate VLAN membership information between participating switches.

MVRP PDUs are sent as multicast.

## Q-in-Q

Extends a L2 Ethernet connection between multiple customers sites. Encapsulate 802.1q traffic into a single, provider 802.1q frame, double tagging traffic.

Used to reduce the MAC and VLAN scale on provider networks.

- S-VLAN : Controlled by the service provider
- C-VLAN : Controlled by the customer

Q-in-Q configured on edge provider devices. Configured to PUSH or POP tags.

- PUSH encapsulates S-VLAN on customer traffic to send over provider network.
- POP removes S-VLAN to decapsulate and send clean VLAN tagged traffic to customer.

### Layer 2 Protocol Tunneling (L2PT)

L2PT is needed to effectively tunnel L2 protocols, such as RSTP, LACP, and LLDP, across a provider network

## Multiple Spanning-Tree Protocol (MSTP)

Creates multiple spanning-tree instances (MSTI) to provide multiple root paths for different groups of VLANs. This mechanism can be used for L2 traffic load balancing\sharing.

Multiple spanning-tree region is the group of switches to participate in MSTP. MTSP supports up to 64 MSTIs.

CST instance (CSTI == MSTI 0) provide backward compatibility with legacy 802.1D STP as well as join multiple MST regions.

Internal Spanning Tree extends CST into MST regions. MST regions appear as a single virtual bridge to CST.

Important configuration items used to calculate the __Configuration Digest__. This digest must match for switches to exist in the same MST region:

- Configuration Name
- Revision Level
- MSTIs and VLAN mapping
  - Bridge priority can and should be unique to identify the root bridge per MSTI

## VSTP

Review section

## Authenticate Access

Review sections
 