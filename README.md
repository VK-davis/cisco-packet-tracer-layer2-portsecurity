# Switch Configuration with Port Security

## Overview

This repository contains the configuration for a Cisco switch with a focus on port security. The configuration includes port security settings to restrict access based on MAC addresses and ensure that unauthorized devices are blocked. The switch is also configured to have all interfaces in shutdown mode by default.

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
