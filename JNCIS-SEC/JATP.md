# JATP

## Configuration Modes
- CM - Central Manager Mode
- Core
- Diagnosis
    - Ability to run PCAP, SCP files and check the setup settings
    - _setupcheck all_ CLI command will check the state off the system and services
- Server
    - Hareware\System operational commands
        - Interface restart
        - Reboot and shutdown
        - Service restart
        - Configuration restore
        - Appplicance data reset
        - Ping and traceroute
- Wizard
    - CLI question driven wizard for initial setup

## Web GUI

### Configuration menu

Three sections:
1. Notifcations
2. SIEM Settings
3. System Profiles

## Enrolling SRX

Config > System Profiles > SRX Settings
Collect enrollment script from Web GUI, then paste into the SRX appliance to add to JATP

To validate enrollment from SRX: _show services advanced-anti-malware_ command