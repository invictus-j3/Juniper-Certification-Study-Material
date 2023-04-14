# OSPF

## Quick information

- All OSPF routers: 224.0.0.5
- All Designated routers: 224.0.0.6

## OSPF Packet Types

- Type 1 - Hello
  - Multicast to 224.0.0.5
  - Used to setup and maintain OSPF neighbor-ship
  - Default timer to send every 10 seconds
  - Hello Packet Header
    - Network Mask*
    - Hello Interval*
    - Dead Interval*
    - Options*
    - Router Priority
    - Designated Router
    - Backup Designated Router
    - Neighbor

        _"*" = values must match for neighbors to form (exept network mask on p2p links)_
- Type 2 - Database Description (DD packets)
  - Used only on initial neighbor-ship formation
  - Two main uses
    - Determine which router is in charge of DB exchange
      - Router with HIGHEST router id is master
      - Transfer LSA headers
        - 24 bytes in length
- Type 3 - Link-State Request
  - Request for DB version and any missing information
  - 24 bytes
- Type 4 - Link-State Update
  - Main way that OSPF shares information
  - Can contain many LSA
  - Update sent in one of two ways
    - In response to LS request packet
    - If information about a specific link changes after adjacency
    - 24 bytes
- Type 5 - Link-State Acknowledgement
  - Sent as unicast
  - Can respond to multiple LSAs
    - The LSAs responded to are found in the packet header

## OSPF Adjacency States

- Down
  - Waiting for a start event
- Init
  - Possible OSPF neighbor is seen via new hello packet
  - Type 1 packet
- 2way
  - OSPF neighbors have both seen hello packets with router IDs
  - Bidirectional communication is good
  - No LSAs have been sent
  - Type 1 packet
- ExStart
  - Begin database description process
  - Negotiation of which router is master for DB sync
  - Type 2 packet
- Exchange
  - Exchanging LSA headers
  - Type 3 - 5 packets
- Loading
  - All LSAs have been sent, but peer is still receiving DB info
- Full
  - DBs are fully synced
  - Mostly type 1 packets
  - Can send type 3 - 5 packets

## OSPF Adjacency Optimization

- When connected over a broadcast link (Ethernet for example), OSPF can optimize advertising routers
  - Remember that each router would broadcast the same information so this is a way to summarize
  - DR
    - Designated Router
    - Forms adjacencies to all routers on the link
    - Router represents the broadcast link for all OSPF neighbors
    - Advertise LS info to the rest of the AS
  - BDR
    - Backup Designated Router
    - Also forms adjacencies to all routers on the link
    - Does not send any LSAs
  - DROther
    - Only forms full adjacencies to the DR and BDR
    - Forms 2way neighbor state with other DROther routers
- Point to point type links do not have DR\BDR process
  - Can save up to 40 seconds of time bypassing process
  - No type 2 LSA is generated on this link
- DR Election process
  - Priority
    - Higher value wins
    - Value from 0 - 255
    - 0 means router is not eligible for DR
    - Junos default is 128
  - Router ID
    - Highest router ID wins
  - DR state is not preemptive
    - Once elected, a DR will not change even is a higher priority router comes on line
  - DR election takes 40 seconds
  - BDR election follows the same rules
    - Monitors DR
    - Takes over if there's a DR failure
    - Once elected, new, higher priority routers will not become BDRs

## OSPF Areas

- Stub Area
  - Does not carry external routes
  - Cannot have ASBRs
- Not-So-Stubby Area
  - Like a stub area, but
  - Allows external routes to be advertised from the area
- Totally Stubby Area
  - Receives only a default route from the backbone

## OSPF Area Routes

- Intra-Area Routes
  - Routes that originate from within an area
- Inter-area Routes
  - Routes that originate from within OSPF, but from another area
  - Also called summary routes
- External
  - Routes re-advertised from outside of OSPF
    - E1 (External Type 1): Inherits cost of external metric plus the internal costs
      - E1 routes are preferred when equal E1 and E2 routes exist
    - E2 (External Type 2): Inherits cost of external metric and does not add cost
      - Default for Junos, must be modified by policy

## SPF LSA Types

- Type 1 - Router Links
  - Detail interfaces and neighbors within the same area
- Type 2 - Network Links
  - Detail a connected Ethernet segment
    - Sent by the DR within the same area
- Type 3 - Summary Links
  - Detail IP prefixes learned from router (T1) and network (T2)
  - Make up the information sent to other OSPF areas
    - Sent by the ABR
- Type 4 - ASBR Summary Links
  - Detail router ID of ASBRs located in other areas
  - Send by the ABRs where the ASBR is located
- Type 5 - External Links
  - Details prefixes redistributed into OSPF
- Type 7 - NSSA External Links
  - Similar to type 5
  - Translated to type 5 by the ABR for all other areas
