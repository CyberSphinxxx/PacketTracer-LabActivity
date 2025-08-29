

### Topology Recap
- **Devices**:
  - PCs: PC0, PC1 (connected to S1), PC2, PC3 (connected to S2)
  - Switches: S1 (left), S2 (right)
  - Routers: R1 (connected to S1), R2 (connected to S2), R1-R2 link
- **Networks**:
  - S1 LAN: 192.168.10.0/24, 2001:DB8:ACAD:10::/64
  - S2 LAN: 10.1.1.0/24, 2001:DB8:CAFE:1::/64
  - R1-R2 Link: 209.165.200.224/30, 2001:DB8:FEED:224::/64

### Adjustments for IPv6 on Switches
- Since S1 and S2 (Cisco 2960 with LAN Base) don’t support IPv6, we’ll configure their VLAN 1 interfaces with IPv4 only.
- The PCs, R1, and R2 will still use IPv6, but communication through the switches will rely on IPv4.

### Step 1: Add and Connect Components in Packet Tracer
1. **Add Devices**:
   - 4 PCs: PC0, PC1, PC2, PC3
   - 2 Switches: S1 (2960-24TT), S2 (2960-24TT)
   - 2 Routers: R1 (ISR 4331), R2 (ISR 4331)
2. **Connect Devices** (use copper straight-through cables):
   - PC0 to S1 (FastEthernet0/1)
   - PC1 to S1 (FastEthernet0/2)
   - S1 (FastEthernet0/24) to R1 (GigabitEthernet0/0/0)
   - R1 (GigabitEthernet0/0/1) to R2 (GigabitEthernet0/0/0)
   - R2 (GigabitEthernet0/0/1) to S2 (FastEthernet0/24)
   - PC2 to S2 (FastEthernet0/1)
   - PC3 to S2 (FastEthernet0/2)

### Step 2: Configure Each Component
Below are the full CLI commands for each device. Copy and paste them into the CLI of the respective device. I’ve also included PC configurations (for Packet Tracer’s Desktop > IP Configuration).

#### PC0 (Connected to S1)
- **IPv4**: 192.168.10.2/24, Gateway: 192.168.10.10 (R1’s Gig0/0/0)
- **IPv6**: 2001:DB8:ACAD:10::2/64, Gateway: 2001:DB8:ACAD:10::10
- In Packet Tracer:
  - Go to PC0 > Desktop > IP Configuration
  - Set:
    - IPv4 Address: 192.168.10.2
    - Subnet Mask: 255.255.255.0
    - Default Gateway: 192.168.10.10
    - IPv6 Address: 2001:DB8:ACAD:10::2
    - IPv6 Gateway: 2001:DB8:ACAD:10::10

#### PC1 (Connected to S1)
- **IPv4**: 192.168.10.3/24, Gateway: 192.168.10.10
- **IPv6**: 2001:DB8:ACAD:10::3/64, Gateway: 2001:DB8:ACAD:10::10
- In Packet Tracer:
  - Go to PC1 > Desktop > IP Configuration
  - Set:
    - IPv4 Address: 192.168.10.3
    - Subnet Mask: 255.255.255.0
    - Default Gateway: 192.168.10.10
    - IPv6 Address: 2001:DB8:ACAD:10::3
    - IPv6 Gateway: 2001:DB8:ACAD:10::10

#### PC2 (Connected to S2)
- **IPv4**: 10.1.1.2/24, Gateway: 10.1.1.10 (R2’s Gig0/0/1)
- **IPv6**: 2001:DB8:CAFE:1::2/64, Gateway: 2001:DB8:CAFE:1::10
- In Packet Tracer:
  - Go to PC2 > Desktop > IP Configuration
  - Set:
    - IPv4 Address: 10.1.1.2
    - Subnet Mask: 255.255.255.0
    - Default Gateway: 10.1.1.10
    - IPv6 Address: 2001:DB8:CAFE:1::2
    - IPv6 Gateway: 2001:DB8:CAFE:1::10

#### PC3 (Connected to S2)
- **IPv4**: 10.1.1.3/24, Gateway: 10.1.1.10
- **IPv6**: 2001:DB8:CAFE:1::3/64, Gateway: 2001:DB8:CAFE:1::10
- In Packet Tracer:
  - Go to PC3 > Desktop > IP Configuration
  - Set:
    - IPv4 Address: 10.1.1.3
    - Subnet Mask: 255.255.255.0
    - Default Gateway: 10.1.1.10
    - IPv6 Address: 2001:DB8:CAFE:1::3
    - IPv6 Gateway: 2001:DB8:CAFE:1::10

#### Switch S1 (Left-Side Switch)
- **VLAN 1**: 192.168.10.1/24 (no IPv6 since it’s not supported)
- **Security**: Passwords and banner
```
enable
configure terminal
hostname S1
interface vlan 1
ip address 192.168.10.1 255.255.255.0
no shutdown
exit
interface FastEthernet0/24
switchport mode access
switchport access vlan 1
no shutdown
exit
enable secret cisco123
line console 0
password conpass
login
exit
line vty 0 15
password vtypass
login
exit
banner motd #Unauthorized access is prohibited!#
service password-encryption
exit
copy running-config startup-config
```

#### Switch S2 (Right-Side Switch)
- **VLAN 1**: 10.1.1.1/24 (no IPv6 since it’s not supported)
- **Security**: Passwords and banner
```
enable
configure terminal
hostname S2
interface vlan 1
ip address 10.1.1.1 255.255.255.0
no shutdown
exit
interface FastEthernet0/24
switchport mode access
switchport access vlan 1
no shutdown
exit
enable secret cisco123
line console 0
password conpass
login
exit
line vty 0 15
password vtypass
login
exit
banner motd #Unauthorized access is prohibited!#
service password-encryption
exit
copy running-config startup-config
```

#### Router R1
- **Gig0/0/0** (to S1): 192.168.10.10/24, 2001:DB8:ACAD:10::10/64
- **Gig0/0/1** (to R2): 209.165.200.225/30, 2001:DB8:FEED:224::1/64
- **Static Routes**:
  - IPv4: 10.1.1.0/24 via 209.165.200.226
  - IPv6: 2001:DB8:CAFE:1::/64 via 2001:DB8:FEED:224::2
```
enable
configure terminal
hostname R1
interface GigabitEthernet0/0/0
ip address 192.168.10.10 255.255.255.0
ipv6 address 2001:DB8:ACAD:10::10/64
no shutdown
exit
interface GigabitEthernet0/0/1
ip address 209.165.200.225 255.255.255.252
ipv6 address 2001:DB8:FEED:224::1/64
no shutdown
exit
ip route 10.1.1.0 255.255.255.0 209.165.200.226
ipv6 route 2001:DB8:CAFE:1::/64 2001:DB8:FEED:224::2
enable secret cisco123
line console 0
password conpass
login
exit
line vty 0 15
password vtypass
login
exit
banner motd #Unauthorized access is prohibited!#
service password-encryption
exit
copy running-config startup-config
```

#### Router R2
- **Gig0/0/0** (to R1): 209.165.200.226/30, 2001:DB8:FEED:224::2/64
- **Gig0/0/1** (to S2): 10.1.1.10/24, 2001:DB8:CAFE:1::10/64
- **Static Routes**:
  - IPv4: 192.168.10.0/24 via 209.165.200.225
  - IPv6: 2001:DB8:ACAD:10::/64 via 2001:DB8:FEED:224::1
```
enable
configure terminal
hostname R2
interface GigabitEthernet0/0/0
ip address 209.165.200.226 255.255.255.252
ipv6 address 2001:DB8:FEED:224::2/64
no shutdown
exit
interface GigabitEthernet0/0/1
ip address 10.1.1.10 255.255.255.0
ipv6 address 2001:DB8:CAFE:1::10/64
no shutdown
exit
ip route 192.168.10.0 255.255.255.0 209.165.200.225
ipv6 route 2001:DB8:ACAD:10::/64 2001:DB8:FEED:224::1
enable secret cisco123
line console 0
password conpass
login
exit
line vty 0 15
password vtypass
login
exit
banner motd #Unauthorized access is prohibited!#
service password-encryption
exit
copy running-config startup-config
```

### Step 3: Test Connectivity
After applying all configurations:
1. **Ping Between Routers**:
   - On R1: `ping 209.165.200.226`
   - On R2: `ping 209.165.200.225`
2. **Ping Between PCs (IPv4)**:
   - On PC0: `ping 10.1.1.2` (PC2)
   - On PC2: `ping 192.168.10.2` (PC0)
   - On PC1: `ping 10.1.1.3` (PC3)
   - On PC3: `ping 192.168.10.3` (PC1)
3. **Check Routing Tables**:
   - On R1: `show ip route`, `show ipv6 route`
   - On R2: `show ip route`, `show ipv6 route`

### Notes
- **IPv6 Limitation**: Since S1 and S2 don’t support IPv6, end-to-end IPv6 pings (e.g., PC0 to PC2 using IPv6) won’t work. IPv4 should work fine.
- **Security**: The configurations include passwords and banners as specified.
- **Troubleshooting**: If pings fail, double-check:
  - Interface statuses: `show ip interface brief` on all devices.
  - Routing: Ensure static routes are in the routing tables.
  - Physical connections: Verify cables and port assignments in Packet Tracer.