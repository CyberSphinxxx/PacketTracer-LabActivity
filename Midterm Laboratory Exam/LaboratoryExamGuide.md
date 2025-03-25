Here are concise and organized notes to help you prepare for your hands-on lab exam based on the provided details. These notes focus on key steps and configurations for quick reference during the exam.

---

### Lab Exam Notes: Network Configuration

**Objective:** Configure end devices, switches, and routers with IPv4/IPv6 addressing, static routing, security settings, and hostnames per the topology.

---

### Step 1: Configure IP Addresses on End Devices (PCs)
- **PC0:**
  - IPv4: 192.168.10.2 / 255.255.255.0 / Gateway: 192.168.10.1
  - IPv6: 2001:DB8:ACAD:10::2/64 / Gateway: 2001:DB8:ACAD:10::1
- **PC1:**
  - IPv4: 192.168.10.3 / 255.255.255.0 / Gateway: 192.168.10.1
  - IPv6: 2001:DB8:ACAD:10::3/64 / Gateway: 2001:DB8:ACAD:10::1
- **PC2:**
  - IPv4: 10.1.1.2 / 255.255.255.0 / Gateway: 10.1.1.1
  - IPv6: 2001:DB8:CAFE:1::2/64 / Gateway: 2001:DB8:CAFE:1::1
- **PC3:**
  - IPv4: 10.1.1.3 / 255.255.255.0 / Gateway: 10.1.1.1
  - IPv6: 2001:DB8:CAFE:1::3/64 / Gateway: 2001:DB8:CAFE:1::1
- **How:** Control Panel > Network > Ethernet Properties > Set IPv4/IPv6.

---

### Step 2: Configure Switches (S1 & S2)
- **S1 (Left):**
  ```
  enable
  configure terminal
  hostname S1
  sdm prefer dual-ipv4-and-ipv6 default
  interface vlan 1
   ip address 192.168.10.4 255.255.255.0
   ipv6 address 2001:DB8:ACAD:10::4/64
   no shutdown
  ip default-gateway 192.168.10.1
  ip default-gateway 2001:DB8:ACAD:10::1
  copy running-config startup-config
  ```
- **S2 (Right):**
  ```
  enable
  configure terminal
  hostname S2
  sdm prefer dual-ipv4-and-ipv6 default
  interface vlan 1
   ip address 10.1.1.4 255.255.255.0
   ipv6 address 2001:DB8:CAFE:1::4/64
   no shutdown
  ip default-gateway 10.1.1.1
  ip default-gateway 2001:DB8:CAFE:1::1
  copy running-config startup-config
  ```
- **How:** Use console cable, PuTTY/Tera Term, Serial connection.

---

### Step 3: Configure Routers (R1 & R2)
- **R1:**
  ```
  enable
  configure terminal
  hostname R1
  interface Gig0/0
   ip address 192.168.10.1 255.255.255.0
   ipv6 address 2001:DB8:ACAD:10::1/64
   no shutdown
  interface Gig0/1
   ip address 209.165.200.225 255.255.255.252
   ipv6 address 2001:DB8:FEED:224::1/64
   no shutdown
  copy running-config startup-config
  ```
- **R2:**
  ```
  enable
  configure terminal
  hostname R2
  interface Gig0/1
   ip address 209.165.200.226 255.255.255.252
   ipv6 address 2001:DB8:FEED:224::2/64
   no shutdown
  interface Gig0/0
   ip address 10.1.1.1 255.255.255.0
   ipv6 address 2001:DB8:CAFE:1::1/64
   no shutdown
  copy running-config startup-config
  ```
- **How:** Use console cable, PuTTY/Tera Term, Serial connection.

---

### Step 4: Configure Static Routes
- **R1:**
  ```
  ipv6 unicast-routing
  ip route 10.1.1.0 255.255.255.0 209.165.200.226
  ipv6 route 2001:DB8:CAFE:1::/64 2001:DB8:FEED:224::2
  ```
- **R2:**
  ```
  ipv6 unicast-routing
  ip route 192.168.10.0 255.255.255.0 209.165.200.225
  ipv6 route 2001:DB8:ACAD:10::/64 2001:DB8:FEED:224::1
  ```

---

### Step 5: Security Configurations (S1, S2, R1, R2)
```
enable secret cisco123
line console 0
 password conpass
 login
line vty 0 15
 password vtypass
 login
banner motd #Unauthorized access is prohibited!#
service password-encryption
copy running-config startup-config
```

---

### Step 6: Verify Configurations
- **R1 & R2:** `show running-config`
- **S1 & S2:** `show running-config`

---

### Step 7: Test Connectivity
- **Ping Tests:**
  - PC0 → PC2: 10.1.1.2 & 2001:DB8:CAFE:1::2
  - PC2 → PC0: 192.168.10.2 & 2001:DB8:ACAD:10::2
  - PC1 → PC3: 10.1.1.3 & 2001:DB8:CAFE:1::3
  - PC3 → PC1: 192.168.10.3 & 2001:DB8:ACAD:10::3
- **Routing Tables:**
  - R1: `show ip route` & `show ipv6 route`
  - R2: `show ip route` & `show ipv6 route`

---

### Quick Tips
1. Double-check IP addresses and subnet masks.
2. Ensure `no shutdown` is applied to interfaces.
3. Save configs frequently (`copy run start`).
4. Test IPv4 and IPv6 separately for full connectivity.

---

Good luck on your lab exam! Let me know if you need further clarification or additional notes.