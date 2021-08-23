# IS-IS

## Theory
- IS-IS = Intermediate system to intermediate system
- A Router is an intermediate system
    - Only belongs to a single area
    - Level 1 Router
        - Station (end device) router
        - Maintains routing information for the area in which they belong
    - Level 2 Router
        - Maintains routes for the backbone 
    - Level 1\2 Routers are used to move between areas
    - Level 1 routers used L1\L2 routers to leave the network
- IS-IS metric is user defined value from 0-63
    - 10 is the default value
    - Allows for maximum path cost of 1023
- Adjacency Requirements
    - Interface MTU match
    - Levels match
    - System IDs must be unique
    - Authentication
- Hello Timer = 10 seconds
### Routing Domains
- Level 0
    - Identifies end system (ES)
    - ES-IS, similar to an OSPF stuf area
- Level 1
    - L1 routers in the same area
    - Intra area routes
- Level 2
    - Routing between areas, inter area routing
- Level 3
    - External routing out of the network domain
    - Inter AS routing
### NSAP Addressing
- Originaly broken into 5 parts
    - AFI - Athority and Format Identifier
    - IDI - Initial Domain Identifier
    - HODSP - High Order DSP
    - System ID
    - SEL - NSAP Selector
- Modern format condenses down to three values 
    - Area
        - Veriable length up to total of 13 bytes
        - AFI
        - IDI
        - HODSP
    - System ID
        - 6 bytes
    - SEL
        - 1 byte
    - Example:
        - 46.0001.0192.0168.1010.00

## Additional Notes
- show isis adjacency
    - verses neighbor