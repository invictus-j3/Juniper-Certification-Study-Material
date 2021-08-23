# Junos SRX IPS

## General Notes

- Junos device security options
    - Stateless firewall filters
    - Screen options
        - Operate at layer 3 and layer 4
        - Fight agains DDOS type attacks
        - Statistics based
            - Examples:
                - ICMP flood
                - UDP flood
                - TCP SYN flood source and destination
                - TCP port scan
                - ICMP IP sweep
        - Signature based
            - Examples:
                - TCP Winnuke
                - TCP SYN fragment
                - TCP no flag
                - TCP SYN FIN
                - TCJ land
                IP bad option
                IP unknown protocol
    - Security policies
    - Unified threat management
        - Layer 7 threat mitigation
        - In stream analyzing: AV, antispam, content filtering, and web filtering
        - Protocols:
            - SMTP
            - POP3
            - IMAP4
            - HTTP
            - FTP
    - IPS

## IPS

Rulebase evaluation of rules occures in order. Traffic is compared to all rules in the IPS rulebase, execpt for a match to a terminal or exempt rule. An attack can match multiple rules and the most severe action applies. Traffic matching _Terminal_ rules will not be evaluated by any further rules.

Rule Action Severety (_most severe to least severe_):
1. Close
2. Drop
3. Diffserv
4. Ignore
5. No action

IPS Engine Rule Actions:
1. No Action
    - No action other than creating an optional entry in the log
2. Ignore Action
    - Stop scanning traffic for this flow
3. Drop Packet
    - Drops the attack packet(s), but leaves the connection open (silent discard)
    - Does not send RSTs to client or server
    - Additional packets are allowed
4. Drop Connection
    - Like Drop Packet, but will drop all other packets in the flow
    - Still leaves the connection open
5. Close Client
    - RST packet sent to the __sending__ host
6. Close Server
    - RST packet sent to the __receiveing__ host
7. Close client and Server
    - RST packet sent to __both__ the sending and receiving hosts
8. Recommended
9. Mark Differentiated Services
    - Adds DSCP to _attacking_ IP packet 
    - Ignores all other packets in the flow 
---
### General Notes:
- Also Layer 7 thread mitigation
- The signature database can be updated automatically
- Includes customizable policy templates
- Signature based and protocol anomaly based protection
- Protects against known Microsoft vulnerabilities
---
### IPS IP Actions
IP Actions can be used to mark IP addresses invovled in an attack, then take action on future flows. The Action types are:
1. None
2. IP Block
    - Silent drop
3. IP Close
    - Active drop, RST sent
4. IP Notify
    - Log entry created

### Unified IPS Policies

Unified security policies can be confiugred with multiple IPS polices. A default policy can be confgured so that any policy conflict is resolved by using the default policy.

Once a final layer 7 application is identified, a relookup is performed to attempt new IPS policy match\actions.