# JNCIP-ENT - Advanced Junos Enterprise Switching

## VLAN Traffic Management

| Port Mode      | Ether-type |
| -------------- | ---------- |
| Access         | 0x0800     |
| Trunk          | 0x8100     |
| S-VLAN (Q-in-Q)| 0x88A8     |
| C-VLAN (Q-in-Q)| 0x8100     |

## Private VLANS (P-VLANS)

Splits a broadcast domain into multiple isolated broadcast subdomains.

Consists of a primary VLAN and nested secondary VLANs

Secondary VLAN types are `community` and `isolated`. Many community VLANs can exists that can send traffic between hosts on the same community VLAN and primary VLAN. Isolated VLANs can ONLY receive traffic from primary VLAN. Only one isolated VLAN can exist.

### Secondary VLANs

| VLAN Type | Designations |
| --- | --- |
| Community | Enables only deviced within the same community VLAN to commuicate |
| Isolated | Can only communicate withe the Primary VLAN |
| Interswitch Isolated | Moves Isolated and Community VLAN traffic between switches |

### Private VLAN Port Types

| Port Type | Designations |
| --- | --- |
| Promiscuous | Trunk ports typically connect to a router, Layer 2 connectivity to all other switch ports |
| Community | Ports that belong to a community |
| Isolated | Add info here |
| PVLAN Trunk | Connect two switches participating in the same PVLAN, communicate with all ports except isolated |

Promiscuous and trunk ports must include `inter-switch-link` configuration on both sides. Inter switch links are configured with only the Primary VLAN.

### P-VLAN Configuration Elements

The Primary VLAN uses the `isolated-vlan [ vlan ]` and `community-vlans [ vlan(s) ]` to apply the Secondary VLANs.

```junos title="Primary VLAN Configuration"
[edit vlans]
<primary_vlan_name>
    isolated-vlan <isolated_vlan_name>
    community-vlans <community_vlan_name(s)>
```

Secondary VLANs are configured with the `private-vlan [ community | isolated ]` statement.

```junos title="Secondary VLAN Configuration"
[edit vlans]
<secondary_vlan_name>
    private-vlan <isolated | community>
```

Access interfaces are configured with the appropirate Secondary VLANs.

```junos title="P-VLAN Access Port Configuration"
[edit interfaces]
ge-x/y/z {
  unit 0 family ethernet-switching {
    interface-mode access;
    vlan {
      members <isolated_vlan_name | community_vlan_name>
    }
  }
}
```

For interfaces connecting switches, configure a trunk interface with the `inter-switch-link` flag and just the primary VLAN assigned.

```junos title="Inter-Switch-Link Configuration"
[edit interfaces]
ge-x/y/z {
  unit 0 family ethernet-switching {
    interface-mode trunk;
    inter-switch-link;
    vlan {
      members <primary_vlan_name>
    }
  }
}
```

Promiscuous ports are configured as normal trunk ports.

## Multiple VLAN Registration Protocol (MVRP)

MVRP is part of the Multiple Registration Protocol (MRP - 802.1ak). It is be used to dynamically create and prune VLANs over trunk links. Standard version of Cisco's VTP. These newer protocols replace GARP and GVRP.

Uses PDUs, which contain MRP messages, to communicate VLAN membership information between participating switches over trunk ports.

MVRP PDUs are sent as multicast. It works with Rapid Spanning Tree Protocol (RSTP) and Multipe Spanning Tree Protocol (MSTP), but not VLAN Spanning Tree Protocol (VSTP).

### MVRP Messages

Message State Definitions:

- __Registered__ VLANs are activily participating in the network. Affects VLAN membership and traffic
- __Declared__ VLANs that have been communicated to other MVRP members. Provides information to other devices.

| Message | VLAN Information State |
| --- | --- |
| Empty | No VLAN information is declared or registered |
| In | VLAN information is registered but not declared |
| JoinEmpty | VLAN information is declared but not registered |
| JoinIn | VLAN information is both declared and registered |
| Leave | Previously registered VLAN information is being withdrawn |
| LeaveAll | All registrations are being removed, and participants must re-register if they want to continue with MVRP |
| New | New VLAN information that might not have been registered before |

### MVRP Configuration Elements

MVRP is configured unter the protocols heading and is enabled on uplink\trunk ports only.

```junos title="MVRP Configuration"
[edit protocols]
mvrp {
  interface ge-x/y/z.0;
}
```

Include the `no-dynamic-vlan` command to disable dynamic vlan function.

There are three timers that are critical for MVRP to function.

| Timer Name | Purpose | Default Timer | Range Values |
| --- | --- | --- | --- |
| join | Join timer interval | 200 ms | 100 - 500 ms |
| leave | Leave timer interval | 800 ms | 300 - 800 ms |
| leaveall | Leave-all timer interval | 10 seconds | 10 - 60 seconds |

## Q-in-Q (802.1ad)

Extends a L2 Ethernet connection between multiple customers sites. Encapsulate 802.1q traffic into a single, provider 802.1q frame, double tagging traffic.

Used to reduce the MAC and VLAN scale on provider networks.

- S-VLAN : Controlled by the service provider
- C-VLAN : Controlled by the customer

S-VLAN headers are similar to standard 802.1q headers, except that the Cononical Format Indicator (CFI) field is repurposed as the Deop Eligibility Indicator (DEI) field, used to identify if a frame can be dropped.

There are two important port types on a Q-in-Q Edge Provider Bridge.

- Customer Edge - manage customer tagged frames
- Provider Netwrok - Trunk ports that PUSH or POP S-VLANs

Q-in-Q configured on edge provider devices. Configured to PUSH, POP, or SWAP tags.

- PUSH encapsulates S-VLAN on customer traffic to send over provider network
- POP removes S-VLAN to decapsulate and send clean VLAN tagged traffic to customer
- SWAP switches the S-VLAN and C-VLAN
  - The respective VLANs must be swapped again as the frame egresses the provider network

### Q-in-Q Configurations

Customer Edge port should be configured with the `flexible-vlan-tagging` and `encapsulation extended-vlan-bridge` commands. Then, under the logical unit, C-VLANs should be configured, along with PUSH, POP, or SWAP options.

```junos title="Customer Edge port configuration"
[edit interfaces]
ge-a/b/c {
  flexible-vlan-tagging
  encapsulation extended-vlan-bridge
  unit <ce_unit_number> {
    vlan-id-list <vlan_range>;
    input-vlan-map push;
    output-vlan-map pop;
  }
}
```

The Provider Network port should be configured with the same ethernet services enabled. A unique logical unit per customer should be tagged with the customer S-VLAN.

```junos title="Provider Network port configuration"
[edit interfaces]
ge-x/y/z {
  flexible-vlan-tagging
  encapsulation extended-vlan-bridge
  unit <pn_unit_number> {
    vlan-id <s-tag>
  }
}
```

Respective Customer Edge ports and Provider Network ports are tied together under the VLAN configuration.

```junos title="VLAN configuration"
[edit vlans]
<Q-in-Q_bridge> {
  interface ge-a/b/c.<ce_unit_number>
  interface ge-x/y/z.<pn_unit_number>
}
```

### Layer 2 Protocol Tunneling (L2PT)

L2PT is needed to effectively tunnel L2 protocols across a provider network. L2PT disables protocol fuction on Customer Edge ports and rewrites the MAC address for each protocol to be forwarded over the provider network.

| Supported Protocols |
| :---: |
| 802.1X Authentication |
| Spannint Tree Protocol - RSTP, VSTP, MSTP, STP |
| Link Aggregation Control Protocol (LACP) |
| Discovery Protocols - LLDP, CDP |
| 802.3ah - Opperation Administration and Maintenance (OAM) |
| Link Fault Management (LFM) |
| Other Cisco Propriatary Protocols such as UDLD and VTP |

### L2PT Configuration Elements

L2PT is configured at the protocol level higharchy.

```junos title="L2PT configuration"
[edit protocols]
layer2-control mac-rewrite {
  interface ge-x/y/z protocol <specify protocol to tunnel>
}
```

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

## Power over Ethernet (PoE)

Type of PoE Devices:

- PSE : Power Sourcing Equipment
- PD : Powered Device

PoE Power Budget is the total amount of power that can allocated to all powered devices (PD) connected to a switch (PSE). PoE budget is a function of Power Supply Units (PSU) installed.

PoE is delivered via two modes, __static__ or __class__.

Class is based on standards.

### __Power Classification Summary__

| Class | Maximum Power (at PSE) | Power Range of PD |
| --- | :---: | :---: |
| 0 | 15.4 W | 0.44 through 12.95 W |
| 1 | 4.0 W | 0.44 through 3.84 W |
| 2 | 7.0 W | 3.84 through 6.49 W |
| 3 | 15.4 W | 6.49 through 12.95 W |
| 4 | 30.0 W | 12.95 through 25.5 W |

## Link Layer Discovery Protocol (LLDP)

Layer 2 neighbor discovery protocol. Allows devices to share information. Two versions, __LLDP__ and __LLDP-MED__, and extension of LLDP.

LLDP-MED == Media Endpoint Discovery. Primarily used to identify IP phones, for example to handle emergency services.

Information is defined by TLVs.

TLVs sent every 30 seconds by default. Database entires are purged after 120 seconds of no messages.

## Class of Service (CoS)
