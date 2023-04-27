# Switching

## Basics

Bridging mechanisms:

1. Learning
2. Forwarding
3. Flooding
4. Filtering
5. Aging

### Learning

MAC addresses are learned as frames ingress an interface. Source MAC address is used to populate the ethernet-switching table.

### Forwarding

Frames are forwarded based on the destination MAC address in the L2 header. The ethernet-switching table is used to determine the egress port. Unknown destinations are flooded out of all interfaces except the original ingress interface.

### Flooding

Frames are flooded out all other ports belonging to the same VLAN when:

- Destination MAC is unknown
- Broadcast destination MAC
- Multicast destination MAC and multicast is not configured

### Filtering

Frames are filtered when the destination MAC address has been learned from the ingress port. (Possibly a hub is connected)

### Aging

MAC addresses are aged out after a period of time to prevent stale entries. The default timer is 300 seconds (5 minutes). MAC addresses associated with an interface that has went down are automatically aged out.

## Design and Considerations

### Hierarchical Design

Layers:

- Core
- Aggregation
- Access

Generally, L3 lives at aggregation layer for a broadcast domain via VLAN IRB interfaces. Virtual Chassis used to allow for LAG down to access to create a sort of ethernet fabric.

Pure L3 northbound to Core layer.

Access used to provide access to the network.

## Virtual Chassis Fabric

## EVPN\VXLAN

Standard based, built on IP fabric. Uses a CLOS Spine\Leaf design.

Eliminates broadcasts and STP between spine and leaf layers.
