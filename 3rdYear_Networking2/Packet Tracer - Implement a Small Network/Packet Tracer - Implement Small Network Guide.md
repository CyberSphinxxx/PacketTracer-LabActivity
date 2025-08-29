# Packet Tracer – Implement a Small Network

> **Instructor Note**: Red font color or gray highlights indicate text that appears in the instructor copy only.

---

## Addressing Table

| Device | Interface | Address       | Subnet Mask     | Default Gateway |
|--------|-----------|--------------|----------------|----------------|
| RTA    | G0/0      | 10.10.10.1   | 255.255.255.0  | N/A            |
|        | G0/1      | 10.10.20.1   | 255.255.255.0  | N/A            |
| SW1    | VLAN1     | 10.10.10.2   | 255.255.255.0  | 10.10.10.1     |
| SW2    | VLAN1     | 10.10.20.2   | 255.255.255.0  | 10.10.20.1     |
| PC-1   | NIC       | Any in /24   | 255.255.255.0  | 10.10.10.1     |
| PC-2   | NIC       | Any in /24   | 255.255.255.0  | 10.10.20.1     |

---

## Objectives
- **Part 1**: Create the Network Topology  
- **Part 2**: Configure Devices and Verify Connectivity  

---

## Part 1: Create the Network Topology

### Step 1: Obtain the required devices
1. Select **Network Devices** → Router → **1941 Router** and drag it into the topology.  
2. Select **Switches** → **2960 Switch**, drag two switches into the topology.  
3. Select **End Devices** → **PC**, drag two PCs into the topology.  
4. Arrange the devices logically.  

### Step 2: Name the devices
- Rename devices to match the **Addressing Table**: `RTA`, `SW1`, `SW2`, `PC-1`, `PC-2`.  
- Click the display name below each device to rename.  

### Step 3: Connect the devices
Use **Copper Straight-Through Cables** to connect devices as shown:

| From Device | Port | To Device | Port          |
|-------------|------|-----------|---------------|
| RTA         | G0/0 | SW1       | G0/1          |
| RTA         | G0/1 | SW2       | G0/1          |
| SW1         | F0/1 | PC-1      | FastEthernet0 |
| SW2         | F0/1 | PC-2      | FastEthernet0 |

---

## Part 2: Configure Devices

### Step 1: Configure the Router (RTA)

#### a. Basic Settings
```plaintext
Router> enable
Router# configure terminal
Router(config)# hostname RTA
RTA(config)# enable secret Ciscoenpa55
RTA(config)# line console 0
RTA(config-line)# password Ciscolinepa55
RTA(config-line)# login
RTA(config)# line vty 0 4
RTA(config-line)# password Ciscolinepa55
RTA(config-line)# login
RTA(config)# service password-encryption
RTA(config)# banner motd $The day banner$
```

#### b. Interface Settings
```plaintext
RTA(config)# interface g0/0
RTA(config-if)# ip address 10.10.10.1 255.255.255.0
RTA(config-if)# description Link to SW1
RTA(config-if)# no shutdown

RTA(config)# interface g0/1
RTA(config-if)# ip address 10.10.20.1 255.255.255.0
RTA(config-if)# description Link to SW2
RTA(config-if)# no shutdown
RTA# copy running-config startup-config
```

---

### Step 2: Configure Switches (SW1 & SW2)

#### SW1
```plaintext
Switch> enable
Switch# configure terminal
Switch(config)# hostname SW1
SW1(config)# enable secret Ciscoenpa55
SW1(config)# interface vlan1
SW1(config-if)# ip address 10.10.10.2 255.255.255.0
SW1(config-if)# no shutdown
SW1(config)# ip default-gateway 10.10.10.1
SW1(config)# line console 0
SW1(config-line)# password Ciscolinepa55
SW1(config-line)# login
SW1(config)# line vty 0 4
SW1(config-line)# password Ciscolinepa55
SW1(config-line)# login
SW1# copy running-config startup-config
```

#### SW2
```plaintext
Switch> enable
Switch# configure terminal
Switch(config)# hostname SW2
SW2(config)# enable secret Ciscoenpa55
SW2(config)# interface vlan1
SW2(config-if)# ip address 10.10.20.2 255.255.255.0
SW2(config-if)# no shutdown
SW2(config)# ip default-gateway 10.10.20.1
SW2(config)# banner motd ^CUnauthorized Access Prohibited^C
SW2(config)# line console 0
SW2(config-line)# password Ciscolinepa55
SW2(config-line)# login
SW2(config)# line vty 0 4
SW2(config-line)# password Ciscolinepa55
SW2(config-line)# login
SW2(config)# line vty 5 15
SW2(config-line)# login
SW2# copy running-config startup-config
```

---

### Step 3: Configure the Hosts
- **PC-1**: Assign IP in `10.10.10.0/24` with gateway `10.10.10.1`.  
- **PC-2**: Assign IP in `10.10.20.0/24` with gateway `10.10.20.1`.  

---

## Verification
- Ping between **PC-1** and **PC-2**.  
- Ping from PCs to **RTA** and switches.  
- Ensure all devices can reach each other successfully.  

---
