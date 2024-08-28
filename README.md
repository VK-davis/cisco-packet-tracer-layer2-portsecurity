# Switch Configuration with Port Security

## Overview

This repository contains the configuration for a Cisco switch with a focus on port security. The configuration includes port security settings to restrict access based on MAC addresses and ensure that unauthorized devices are blocked. The switch is also configured to have all interfaces in shutdown mode by default.

## How Port Security Works

Port security is a crucial feature on Cisco switches that helps prevent unauthorized devices from connecting to the network. Here’s an overview of how port security is implemented in this configuration and how it behaves in different scenarios:

### Port Security Overview

- **Sticky MAC Address Learning:** 
  - The switch is configured to learn and remember the MAC addresses of the devices that connect to specific ports. These MAC addresses are stored in the switch's secure MAC address table.
  - The `sticky` feature allows the switch to dynamically learn MAC addresses and add them to the running configuration, ensuring that only known devices can connect to the port.

- **Violation Modes:**
  - In this configuration, the violation mode is set to `restrict`. This means that if an unauthorized device (i.e., a device with a MAC address that is not in the secure MAC address table) tries to connect to a port, the switch will:
    - Block traffic from the unauthorized device.
    - Increment the security violation counter.
    - Log the violation (if logging is enabled).
  - The port remains operational for the allowed devices even when a violation occurs.

### Example Scenario: Attacker Tries to Connect

Imagine the following situation:
1. **Initial Setup:**
   - During the provisioning of devices in a work environment the Network Admin sets up a legitimate PC to be connected to `FastEthernet0/1`. Assume that the switch's MAC table is empty, also assume that the Network Admin already configured the swtich with port security features. After the PC is connected, the switch learns and stores the PC's MAC address using the sticky MAC address feature.

2. **Attacker Action:**
   - One fine day someone with malicious intend physically removes the legitimate PC from say `FastEthernet0/1` and connects their laptop instead.

3. **Port Security Response:**
   - Since the attacker's laptop has a different MAC address, the switch immediately recognizes this as a security violation.
   - Because the violation mode is set to `restrict`(remember, all these settings were configured during the initial provisioning of the switch), the switch blocks the attacker's laptop from sending or receiving any traffic through this port. The switch logs the violation and increments the security counter, but the port itself does not go down.

4. **Reconnecting the Legitimate PC:**
   - If the legitimate PC is reconnected to `FastEthernet0/1`, the switch will recognize its MAC address as a valid, secure address.
   - The switch automatically resumes normal operation, allowing the legitimate PC to communicate on the network without any manual intervention.

### Key Benefits of Port Security in This Setup

- **Automatic Recovery:** If a valid device is disconnected and reconnected, the switch automatically allows it back onto the network without requiring any configuration changes or manual resets.
- **Unauthorized Device Blocking:** Unauthorized devices (like the attacker's laptop in the scenario) are immediately blocked, ensuring that network security is maintained at all times.
- **No Network Downtime:** With the `restrict` violation mode, legitimate devices continue to operate normally even when an unauthorized device is detected, preventing unnecessary network downtime.

This setup demonstrates how port security can be used to protect network integrity and prevent unauthorized access, making it an essential component of a secure network infrastructure.
## Configuration Details

### Port Security Configuration

- **Interfaces FastEthernet0/1, FastEthernet0/2, and FastEthernet0/3** are configured with port security:
  - **Mode:** Access
  - **MAC Address Security:** Sticky
  - **Violation Mode:** Restrict
  - **Specific MAC Addresses Allowed:** 
    - FastEthernet0/1: `000C.85B6.9E42`
    - FastEthernet0/2: `0030.A376.ED71`
    - FastEthernet0/3: `0001.97BD.A4E3`

  This configuration ensures that only devices with the specified MAC addresses can connect to these ports. If an unauthorized device tries to connect, the switch will restrict access and log the violation.

- **All other FastEthernet and GigabitEthernet interfaces** are administratively shut down to prevent unauthorized access until further configuration is applied.

### Current Switch Configuration

```plaintext
version 15.0
no service timestamps log datetime msec
no service timestamps debug datetime msec
no service password-encryption
!
hostname Switch
!
spanning-tree mode pvst
spanning-tree extend system-id
!
interface FastEthernet0/1
 switchport mode access
 switchport port-security
 switchport port-security mac-address sticky 
 switchport port-security violation restrict 
 switchport port-security mac-address sticky 000C.85B6.9E42
!
interface FastEthernet0/2
 switchport mode access
 switchport port-security
 switchport port-security mac-address sticky 
 switchport port-security violation restrict 
 switchport port-security mac-address sticky 0030.A376.ED71
!
interface FastEthernet0/3
 switchport mode access
 switchport port-security
 switchport port-security mac-address sticky 
 switchport port-security violation restrict 
 switchport port-security mac-address sticky 0001.97BD.A4E3
!
interface FastEthernet0/4
 shutdown
!
...
interface FastEthernet0/24
 shutdown
!
interface GigabitEthernet0/1
 shutdown
!
interface GigabitEthernet0/2
 shutdown
!
interface Vlan1
 no ip address
 shutdown
!
line con 0
!
line vty 0 4
 login
line vty 5 15
 login
!
end
```

##Packet Tracer Bugs
- Not seeing any security violations logged but the port security mechanism is working (e.g., the attacker machine cannot ping other devices as intended) but when you run the command, say for e.g., `show port-security interface fa0/1` the `Security Violation Count` remains `0`.
