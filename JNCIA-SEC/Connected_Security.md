# Juniper Connected Security

## Connected Security Principles
- Stronger Security Posture
    - More than just blocking traffic types
    - Secure by user
- Automation Security
    - Leverage automation to react faster and more precisely 
    - Increase security
- Comprehensive Visibility
- Best-in-Class Secure Networking
- Multicloud Ready
- OpenPlatform
    - Third party integrations

## UTM - Unified Threat Management
- Consolidation of several secuirty features
    - Anti-Malware
        - Full File-Based Antivirus
            - Files are reconstructed and scanned agains Kaspersky Lab scan engine
        - Express Antivirus
            - Juniper based pattern matching
            - Less secure
    - Antispam
    - Web Filtering
    - Content Filtering

## Next-Generation Firewall
- App Tracking
    - Understanding security ristks
    - Address new user behavior
- App Firewall
    - Block access to risky apps
    - Allow user-tailored policies
- App QoS
    - Prioritize important apps 
    - Rate-limit less imporant apps
- SSL Proxy
    - SSL packet inspection
- IPS
    - Block security threats

## AppFW - Application Firewall

## JIMS
**Juniper Identity Management Service**

Installed on Windows AD servers to identify users. Used to create user identity policies

## JSA
**Juniper Secuire Anylitics**

SIEM appliance to collect and analyze logging data.

# SRX

## Session-Based Traffic Tracking
- IP
    - Source
    - Destination
- Port
    - Source
    - Destination
- Transport Protocol (TCP\UDP)

## Packet-Mode Processing
To enable packet mode: *set security forwarding-options family mpls mode packet-based*

- *This is because MPLS header comes right after ethernet header, setting is applied to inet as well*

- **Available Features**:
    - Stateless firewall filters
    - CoS
- **Disabled Features**:
    - Stateful firewall processing
    - NAT
    - VPN
    - UTM
    - IDP
    - AppSecure
    - Other advaced security services

## Fast Path Processing
- Screen Options
- TCP
- NAT
- Services\ALG

## SRX Path Processing Order of Operations
### __First Path Processing__
1. Check session table for existing flow
2. Start Session Timer, defaults:
    - 30 minutes for TCP
    - 1 minute for UDP
2. Screen options
3. Destination NAT
4. Route Lookup
    - Determine Ingress and Egress Zone
6. Apply zone policies on both ingress and egress zones
7. Source NAT
8. Apply Advanced Security Services
    - ALG
    - UTM
    - IDP
    - AppSecure
9. Create session in session table for fast path processing

### __Fast-Path Processing__
1. Screen Options
2. TCP
3. NAT (Source\Destination\Static)
4. Apply Advanced Security Services
    - ALG
    - UTM
    - IDP
    - AppSecure

## Valid Security Policy Opperations
- Permit
- Deny
- Reject
- Log
- Count

# Secuirty Zones
- User-Defined
    - Security
    - Functional
        - Used for management access
- System-Defined
    - Null Zone
    - junos-host Zone

# Screen Options
Dynamically detect malicous packets, for example a SYN flood attack. Screen options, if configured, are evaluated first in both first and fast path flows. Very resource efficient.

- **Screen Types**
    - Anomaly-Based
        - Evaulated on a per-packet basis. Examine packet headers, Example: IP options 
    - Flood-Based
        - Evaluated against thresholds and simi-stateful
        - Example are SYN floods or TCP\UDP port scans

## Screen Categories
- ICMP
- IP
- TCP
- UDP

# Address Objects
Are grouped in two categories: Zone and Global. Zone address books will be deprecated in future release. Addresses can be defined in 4 different ways:
- IP Address (/32)
- Wildcare Address - Using a wildcard mask
- Domain Name Address
- Range Address