# SW 1 Config

```bash
enable
configure terminal
spanning-tree portfast default
interface FastEthernet0/1
ip dhcp snooping limit rate 5
switchport mode access
switchport port-security
switchport port-security maximum 4
switchport port-security mac-address sticky
switchport port-security violation restrict
switchport port-security mac-address 0010.11E8.3CBB
spanning-tree portfast
spanning-tree bpduguard enable

interface range FastEthernet0/2, FastEthernet0/10,FastEthernet0/24
ip dhcp snooping limit rate 5
switchport mode access
switchport port-security
switchport port-security maximum 4
switchport port-security mac-address sticky
switchport port-security violation restrict
spanning-tree portfast
spanning-tree bpduguard enable

interface range FastEthernet0/3-9, FastEthernet0/11-23
switchport access vlan 999
shutdown

interface range GigabitEthernet0/1-2
switchport trunk native vlan 100
ip dhcp snooping trust
switchport mode trunk
switchport nonegotiate
vlan 100
name Native
vlan 999
name BlackHole
```
```bash
# SW 2 Config

enable
configure terminal
ip dhcp snooping
ip dhcp snooping vlan 10,20,99
spanning-tree portfast default
interface GigabitEthernet0/1
switchport trunk native vlan 100
switchport mode trunk
switchport nonegotiate

interface GigabitEthernet0/2
switchport trunk native vlan 100
switchport mode trunk
switchport nonegotiate

vlan 100
name Native
```