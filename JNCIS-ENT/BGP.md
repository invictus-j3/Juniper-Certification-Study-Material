# BGP

## Neighbor Connection
TCP\179

## BGP Neighbor States
- Idle
    - Initial state
    - No active neighbor to peer
    - All BGP connection attemps are refused
- Connect
    - Waiting for TCP connection to complete
    - Once connected, OPEN message is sent
    - Opposite of Active
- Active
    - Initiating TCP connection
    - Once connected, OPEN message is sent
    - Opposite of Connect
- OpenSent
    - Waiting for OPEN message
        - If message is received, move to Established
        - Issues result in move to Idle
- OpenConfirm
    - Waiting for KEEPALIVE or NOTIFICATION message
        - Move to Established once received
        - Issues move to Idle
    - Opposite of OpenSent
- Established
    - Start to exachange messages
        - UPDATE
        - NOTIFICATION
        - KEEPALIVE

## BGP Message Details
- Size
    - Max: 4096 octets
    - Min: 19 octets
- Types
    - Open Message
        - Sent once TCP session is established
        - Contains detail about local neighbor and negotiated options
    - Update Message
        - Used to exchange routing information
    - Keepalive Message
        - Used to check if remote neighbor is alive
    - Notification Message
        - Used to notify is something is wrong with session
            - Unsupported option
            - When peer fails to send update or keepalive
    - Refresh Message
        - Allows BGP to readvertise routes
            - Generally not allowed
        - "soft clearing" of session
        - Used more for MPLS based VPNs

## BGP Attributes
- AS Path
    - Well-Known Mandatory
- Local Preference
    - Well-Known Discretionary
- MED
    - Optional Nontransitive
- Origin
    - Well-Known Mandatory
- Next Hop
    - Well-Known Mandatory
- Community
    - Optional Transitive
- Aggregator
    - Optional Transitive
- Atomic Aggregator
    - Well-Known Discretionary
- Cluster List
    - Optional Nontransitive
- Originator ID
    - Optional Nontransitive
### Attribute Definitions
- Well-Known Mandatory
    - Must be supported by all BGP devices and included in all updates
- Well-Known Discretionary
    - Must be supported by all BGP devices, but required in all updates
- Optional Transitive
    - Not requred to support
    - If supported, the data should be passed up changed to peers
- Optional Nontransitive
    - Not requred to support
    - If not recognized, ignored and data no passed to peers

## BGP Attribute Notes
- Next hop
    - eBGP - next hop value is updated by each BGP device
    - iBGP - next hop value is not updated by default
- Local Preference
    - Value only transmitted intra AS
    - Can be set by
        - BGP configuration
        - Routing policy
            - If both are configured, routing policy is prefered
- AS Path
    - Drops route advertisement if router sees own AS in path
        - Prevents loops
- Origin
    - Created by original router
    - Describes where the original router received the route
        - IGP
            - Route value of 0
            - Origin value of 1
        - EGP
            - route value of 1
            - From original EGP protocol
            - Legacy
        - Incomplete
            - Route value of 2
            - Did not come from IGP or EGP
- MED
    - Attempt to influence inbound traffic over multiple links to the same AS
    - Higher the better
    - If value missing, assume 0
- Community
    - Used to tag like prefixes advertized
    - Format: *AS-number:community*
## BGP Path Selection
1. Highest Local-Preference Value
2. Shortest AS-Path
3. Lowest Origin Value
    - Origin: I < E < Incomplete
4. Lowest MED Value
5. EBGP routes over IBGP
6. Best Exit from AS
7. Current active Route for EBGP-Received routes
8. Paths with shortest cluster length
9. Routes with lowest peer ID
## IBGP Route Propagation
- IBGP neighbors do not advertize routes learned from IBGP peers to other IBGP peers
    - Even routes learned from EBGP
    - Forces a full mess IBGP design
- Next Hop route is propigated unchanged
    - Either:
        - ensure external interfaces are reachable
        - configure router to update next hop
            - easier and more advantagious
            - prevents a route from dropping out of route table
## BGP Polcies
- Import polcies
    - Controls how and what routes are imported into the route table, from BGP
- Export polcies
    - Controls how and what routes are exported out of the route table, into BGP