# Module 1: Data Center Design Considerations
Basic DC Environment review.

# Module 2: Ethernet Fabric Architectures
## Virtual Chassis (VC)
- Two types of deployments: **Daisychain** (ring) or **Braided**
- Use Virtual Chassis Ports (VCP) to interconnect VC devices
    - Can use revenue ports as VCPs
    - Can use factory VCPs as revenue ports

Two devices are setup as routing engine (2) or line card (n - 2). Routing engines are setup as master (RE0) and backup (RE1). Active routing engine funtions as control plane device. All device function as forwarding plane devices.

Recommended that uplinks are landed on Linecard devices and not routing engine devices.

Use vme interface, tied to the me interface of the RE0 device, with a single IP address

### Virtual Chassis Control Protocl (VCCP)
Juniper modified IS-IS protocol to facilitate SPF forwarding

## Virtual Chassis Fabric (VCF)
Evolution of VC and runs the same VCCP protocol. Can scale from 4 to 20 devices. RE roles live on spine devices

Leverages a CLOS design (Spine and Leaf)
- Spine devices:
    - QFX5100
    - QFX5110
- Leaf Devices
    - QFX5100
    - QFX5110
    - EX4300

## Junos Fusion
Able to manage many devices as a single device. Similar design (CLOS) as VCF, but uses different protocols.

Different flavors of Fusion:
- Data Center
- Provider Edge
- Enterprise

Data Center version comes in 2 variants:
- MC-LAG (QFX10000)
- EVPN (QFX100002)

**Aggregation devices**
- Run standard Junos
- Fill the role of spine devices
- Fabric type connectvity down to satellite devices on **Cascade Ports**
- Configuration between aggregation devices managed by ICCP\NETCONF
    - ICCP in MC-LAG
    - BGP in EVPN
- Communication to the satellite devices:
    - LLDP
    - 802.1BR
    - JSON formated RPC

**Satellite devices**
Junos is replace with Satellite operating systems (SNOS), managed my aggregation devices. All configuration is pushed down from aggregation. Connected to aggregation devices with **Uplink Ports**. Revenue ports are called **Extended Ports**.

Extended ports opperate in one of two modes:
1. Port Extended Mode
    - All traffic from the extended port is forwarded up to aggregation devices
    - Can make use of features on aggregation device
2. Distributed Forwarding Mode
    - Traffic can be localy switched on satellite device for very low latency applications

Standard juniper switches:
- QFX5100
- QFX5110
- EX4300

# Module 3: IP Fabric Architectures
Remove STP requirements with IP CLOS fabric. Leverage many equal cost paths between spine and leafs. There are a number of different architectures:

1. IGP Only fabirc, **IS-IS** or **OSPF**
    - Works well with smaller scale deployments
    - IGP adjacencies between spine and leaf layers
    - Advertize networks routed off leaf devices
2. IGP + iBGP
    - Use IGP to advertize only loopbacks as network targets
    - Use iBGP to advertize destination networks over IGP
    - Use BGP Route Reflectors (RR) on Spine devices to increase scale
    - Enable **iBGP multipath** and, for the largest scale, **AddPath**
3. eBGP
    - Support the largest data centers
    - Multiple ASN strategies
        - AS per tier
            - All leaf in ASN and all spine in another ASN
            - Saves on ASN exaustion (highly unlikely), but creates issues during failure
        - AS per device
            - Recommended
            - 1023 private ASNs
                - If more devices are required, move from 2 byte to 4 byte ASN
    - Enable eBGP multipath

#### OpenClos
- A python script library available on GitHub to automate configuration of devices in IP fabric

---
## Overlay Networks
1. **MPLSoGRE**
2. **MPLSoUDP**
    - Prefered over MPLSoGRE for ECMP hashing reasons
3. **VxLAN**
    - Encapsulation:
        - IP
            - UDP
                - VxLAN
                - 24 bit VxLAN Network Identifier (VNI)
                    - Frame
    - VxLAN Tunnel Endpoint (VTEP)
        - Source and destination of VxLAN encapsulation
        - Generally a switchport or hypervisor vSwitch
    - VxLAN Gateway
        1. Layer 2 (L2)
            - Generally deployed on Leaf switches, with hypervisors that do not support VxLAN or Bare Metal Servers (BMS)
        2. Layer 3 (L3)
            - Deployed on Spines or hypervisors (with support), or border leafs for Core\WAN routing
    - MAC Learning
        - Data Plane \ Multicast
            - Similar to legacy flooding processes
            - Scale is an issue and have to configure multicast
        - SDN Controller
            - Centralised controller will handle all host learning and inform VTEPs
            - No multicast involved
            - Can be very hypervisor focused
            - Possible issues with BMS and integration with physcial network
        - EVPN
            - Distributed control plane
            - Use iBGP for signaling\learning processes
                - Completely seperate from eBGP underlay
                - Address Family Indicator = 25
                - Subsiquent Address Family Indicator = 75
            - Very fault tolerent, scalable, adaptable and standardized
            - Configuration can be more intensive

# Module 4: Data Center Interconnect
Data Center Interconnect (DCI)

Physical connectivity options:
- Leased Line \ L2 Circuit
    - Use 802.1q for services
- Dark Fiber
    - Use 802.1q or DWDM for services
- DWDM
- L2VPN (VPLS)
    - 802.1q
- L3VPN (MPLS)
    - IP or EVPN/VxLAN
- Internet
    - IPSec or EVPN/VxLAN

# Module 5: Data Center Security
Important Areas of Security
- Policy Enforcement
    - North \ South
    - East \ West
- Segmentation
- Micro-Segmentation
- Parimeter
- SEIM
- AAA

Placement of Security Devices
1. Spine
2. Leaf
    - Dedicate specific leaf switches for interconnection to the rest of the network, WAN for example
    - Border leafs

- In line deployment
    - Cannot bypass secuirty devices
- "One Armed" deployment
    - Security devices are offset from routing devices
    - Traffic controlled by routed devices
    - Conserves unnessessary flow through security devices

Traffic approaches:
| Network | Security | Notes |
| :--- | :---: | ---: |
| Active/Passive | Active/Passive | Reduces HA links, Half of the capacity |
| Active/Active | Active/Passive | Good for high level of East\West traffic |
| Active/Active | Active/Active | Most complex, all interfaces active, North\South traffic |

## Micro Segmentation
Layer 2 - Use multiple vSwitches with the same VLAN-ID, vSRX will connect to all vSwitches

Layer 3 - vSRX provides routing between VLANs

## Juniper Security Tools for the DC
| Name | Purpose |
| :--- | :--- |
| SkyATP | Enhanced Telemetry, Sandbox, Deep inspection (Similar to Wildfire) |
| Policy Enforcer | Automatically react to security incedents, even to the EX switchport |
| Security Director | Centrally manage security policy |
| Log Director | Centralized logging repository |
| JSA (Juniper Secure Analytics) | SIEM, event analytics, compliance |

# Module 6: Virtualization in the Data Center
**SDN Layers**:
- Management
- Control
- Services
    - Any non routing or forwarding service, IPS for example
- Forwarding

**SDN Building Blocks**:
- Forwarding Elements
    - Routers and switches
- SDN Controllers
    - Program the forwarding elements
    - OpenStack, NSX, and Kubernetes are examples of SDN controllers
- OSS\BSS Stack
    - Operstations and Business support system(s)
- Orchestration layer

**SDN Controller Interfaces **
- Northbound
    - Interfaces to OSS\BSS and orchestration tools
- East\West
    - Interfaces between controllers for data sync and failover
- Southbound
    - Interfaces to forwarding elements to be programed by controller

## Contrail
Contrail is an example of an SDN controller. Responsible for creating network connectivity between VMs. vRouters are used by Contrail as a hook into hypervisors to control traffic.

Process flow:
1. Define virtual networks (VN) in Contrail
2. Orchestration tool create VMs to be interconnected
3. Contrail associate VMs to VN, via protocol XMPP
4. Contrail create intra-VN tunnels - update vRouter
5. Contrail create inter-VN tunnels - update vRouter
6. Define routing policy for inter-VN
7. Routes pushed to vRouters

## Contrail Security
vRouter agent communicates with Contrail via XMPP. Defines routing instances of the host hypervisor, FIBs and Flow Tables. When a VM is created, Contrail sends network policy to vRouter agent.

**Security groups** are IPv4 and IPv6 ACLs between VMs and routing instances. **Network Policy** controls how to route between different networks, other hypervisor layers, or externally. Can force traffic through security devices.

# Module 7: Traffic Proritization in the Data Center
Schedulers, via scheduler maps, are applied to all ports in the fabric. Different QoS functions are determend by traffic and direction.

- **Multi Field Classifier**
    - Defined on server facing Leaf interfaces
    - Analyzing Src\Dst IP and Port, possible other details
    - Trying to classify traffic similar to standard QoS process
    - Ultimately assigns traffic to a specific class
- **Rewrite**
    - Northbound traffic from leaf rewrites DSCP (IP) or 802.1p (ethernet) marking
- **Behavior Aggregate Classifiers**
    - Evaluate rewritten, trusted markings to assign traffic to a forwarding class. The occures on all ingrees and egress fabric ports between Spine and Leaf

## Data Center Bridging (DCB)
Focus is to ensure that storage area network (SAN) traffic over IP is free of loss.

**Priority Flow Control (PFC)**
- Use 803.1q markings (CoS) to send pause frames when a specific queue starts to fill up. The allows a RX device time to flush a specific class buffer while still receiving traffic on other queues.

**Enhance Transmission Selection (ETS)**
- Standardized way to schedule queues on a 802.1q basis, similar to schedules on standard switches

**Data Center Bridging Exchange (DCBx)**
- Uses LLDP between devices to negotiate PFC and ETS

# Module 7: High Availablity in the Data Center
## Inter Data Center Resiliency
**Standard IP Engineering**
- Use a supernet and subnet routes to ensure that all prefixes are advertized out of all data centers.

**Anycast**
- Simply advertize the same prefixes out of both data centers and allow routing metrics to bring traffic to the closest data center. More of an active\active model

**Global Server Load Balancer GSLB**
- Use DNS to serve addresses of the Data Center closest to the user
- Most intelegent

## HA in the Data Center
**Virtual Router Redundancy Protocol (VRRP)**
- Use virtual MAC and IP address for default gateway of a network
- Master \ Backup opperation

**Multi-Chassis LAG (MC-LAG)**
- Way to build a LAG between two different devices, providing physical redundancy
- Use ICCP for control between both switches

## Intra-Device Reseliency
Use multiple Routing engines to protect against routing engine failure. Without features, linecards will reboot and failover process is impactful
- Graceful Routing Engine Switchover (GRES)
    - Master RE will keep Standby RE updated will all state information. With RE failure, all processes move over to new RE. Control plane process will flap, causing a slight impact
- Nonstop Routing (NSR) \ Nonstop Bridging (NSS)
    - Compainion protocols to GRES
    - Propigates control plane information from Master to Backup RE
    - Further reduces RE failover, to second to sub-second failover between REs

In Service Software Upgrades (ISSU)
- Used to reduce downtime during a software upgrade
- Requires multiple REs
- Upgrade one RE, failover and repeat

NonStop Software Upgrades (NSSU) (EXVC)
- EX based VC

Topology Independent Upgrdes (QFX Single RE)
- Uses Linux host running on RE, with two VMs running Junos
- Upgrade process similar to ISSU, but with VMs

# Quiz Questions
Device Fan Types. Generally, fans are on the back of a device, so use this to determin type
- AFO
    - **Air Flow _Out_** meaning that the air is flowing out
    - Fans are orange in color
- AFI
    - **Air Flow _In_** meaning that the air is flowing in to the fan
    - Fans are blue in color

Service Now can be used as a Junos Space application to monitor

What is CCC?
What are LDP Layer 2 circuits?
RSVP link protection?
CSPF protocol?