# Chassis Cluster

## Cluster Troubleshooting Procedure

1. Chassis clustering enabled?
2. Hardware the same?
3. Same Junos OS version?
4. Both nodes rebooted?
5. Control plane connection correct and functional?
6. Data plan connection correct and functional?
7. Multiple clusters exist in the same L2 domain?

## Commands to Check Cluster

The following commands can be used to check the operation status of a chassis cluster.

### General Cluster Stats
````
show chassis cluster status
show chassis cluster information
show chassis cluster information configuration-synchronization
show chassis cluster information detail
show chassis cluster information issu
````
### Cluster Interface Stats
````
show chassis cluster statistics
    - Control links heartbeats, fabic link probes and RTO sync
show chassis cluster interfaces
    - Details fxp1, fab, reth cluster interfaces
    - interface monitoring information
show interfaces terse | match fxp
    - High level about control and data link interfaces
show chassis cluster control-plane statistics
    - Control link heartbeats and fabric link
show chassis cluster data-plane statistics
    - RTO sync stats
````
## vSRX Chassis Clustering

vSRX clusters require the same connectivity: management, control link, and data link.

__Management__ interface, like the physical SRX, is the fxp0 interface. Place this interface in the management network.

__Chassis Cluster Links__:
_Control Link_ interface are the __em0__ interfaces. They must be connected together through the vSwitch. Interface requirements are:
- VLAN-ID = 0
- Enable promiscuous mode

_Data Link_ interfaces are the __fab__ interfaces. They also must be connected through the vSwitch. Interface requirements are:
- VLAN-ID = 0
- Jumbo frames (MTU >= 9000)
- For VMware: Security > Effective Policies
    - MAC Address Changes: __Accept__
    - Forged Transmits: __Accept__

### Interface Mapping

Network Adapter | Standalone vSRX | Clustered vSRX
--- | :---: | ---:
1 | fxp0 | fxp0 (node 0 and 1)
2 | ge-0/0/0 | em0 (node 0 and 1)
3 | ge-0/0/1 | ge-_x_/0/0
4 | ge-0/0/1 | ge-_x_/0/1
5 | ge-0/0/2 | ge-_x_/0/2
... | ... | ...
_Continue to 10_ | _node 1, x = 0_ | _node 2, x = 7_


### Difference between SRX and vSRX Clusters

__Node ID and Cluster ID__
- _SRX_ - These values are written into _EEPROM_
- _vSRX_ - Values are written to _/boot/loader.conf_

# cSRX

Container SRX allows for < 1 second restart and bootup times, while supporting the same NGRW services as the vSRX: AppFW, App Track, AppID as well as IPS and UTM.

Only L2 support, using zones (at least 2) and policies tied to said zones. Default zones\policies allow from a trust to untrust zone, but not the reverse. 

Useful in containerized environments. 