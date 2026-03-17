# JNCIP-ENT - Advanced Junos Enterprise Switching

## Private VLANS (P-VLANS)

- Used to divide a bridge domain into multiple smaller domians.
- Made up of a Primary VLAN and one or more Secondary VLANs
- P-VLANs can be spanned across multiple switches
- Voice Vlan and P-VLAN configurations are not compatible
- Types of Secondary VLANs
  - Community VLANs : Enables only deviced within the same community VLAN to commuicate
  - Isolated VLANs : Can only communicate withe the Primary VLAN
  - Interswitch Isolated VLAN : Moves Isolated VLAN traffic between switches
- P-VLAN port types
  - Promiscuous Ports : Trunk port connected to upstream routers. Part of the Primary VLAN and can communicate will all other ports, including Isolated ports
  - Community Ports : Access ports that allow communication within a community VLAN and\or the Promiscous port
  - Isolated Ports : Access ports that are only allowed to communicate with the Promiscuous port
  - P-VLAN Trunk Ports : Trunk port that connects swtiches together. It carries the Primary VLAN and all Secondary VLANs

### Configuration Elements

- Secondary VLANs are configured with the private-vlan [ community | isolated ] statement
- The Primary VLAN uses the isolated-vlan [ vlan ] and community-vlans [ vlans ] to apply the Secondary VLANs
- Access interfaces are configured with the appropirate Secondary VLANs
- For interfaces connecting switches:
  - they need to be configured with:
    - interface-mode trunk
    - inter-switch-link
      - This command configures the port as a P-VLAN trunk port
    - Only the primary VLAN should be configured as a member
- Promiscuous ports are configured as normal trunk ports

## Multiple VLAN Registation Protocol

Is part of the Multiple Registration Protocol (MRP - 802.1ak). MVRP allows for Dynamic creation and\or pruning of VLANs from a trunk port, similar to VTP. These newer protocols replace GARP and GVRP.