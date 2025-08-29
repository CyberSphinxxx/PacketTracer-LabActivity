# Verify Directly Connected Networks (Instructor Version)

## Addressing Table

| Device | Interface      | IP Address / Prefix           | Default Gateway |
|--------|----------------|-------------------------------|-----------------|
| R1     | G0/0/0         | 172.16.20.1/25                | N/A             |
|        | G0/0/1         | 172.16.20.129/25              | N/A             |
|        | S0/1/0         | 209.165.200.225/30            | N/A             |
| PC1    | NIC            | 172.16.20.10/25               | 172.16.20.1     |
| PC2    | NIC            | 172.16.20.138/25              | 172.16.20.129   |
| R2     | G0/0/0         | 2001:db8:c0de:12::1/64       | N/A             |
|        | G0/0/1         | 2001:db8:c0de:13::1/64       | N/A             |
|        | S0/1/1         | 2001:db8:c0de:11::1/64       | N/A             |
|        | fe80::2        | N/A                           | N/A             |
| PC3    | NIC            | 2001:db8:c0de:12::a/64       | fe80::2         |
| PC4    | NIC            | 2001:db8:c0de:13::a/64       | fe80::2         |

## Objectives

- Verify IPv4 Directly Connected Networks
- Verify IPv6 Directly Connected Networks
- Troubleshoot connectivity issues

## Background

Routers R1 and R2 each have two LANs. Your task is to verify the addressing on each device and verify connectivity between the LANs.

> **Note**: The user EXEC password is `cisco`. The privileged EXEC password is `class`.

## Instructions

### Part 1: Verify IPv4 Directly Connected Networks

#### Step 1: Verify IPv4 addresses and port status on R1.

1. **Check the status of the configured interfaces by filtering the output:**

    ```bash
    R1# show ip interface brief | exclude unassigned
    Interface              IP-Address      OK? Method Status                Protocol 
    GigabitEthernet0/0/0   172.16.20.1     YES manual administratively down down 
    GigabitEthernet0/0/1   172.16.20.129   YES manual up                    up 
    Serial0/1/0            209.165.200.229 YES manual up                    up
    ```

    - Based on the output, correct any port status problems that you see. `GigabitEthernet0/0/0` is down, so to enable it:

    ```bash
    R1(config)# interface g0/0/0
    R1(config-if)# no shutdown
    ```

2. **Refer to the Addressing Table and verify the IP addresses configured on R1. Make any corrections to addressing if necessary.**

    ```bash
    R1# show running-config 
    Building configuration...
    Current configuration : 977 bytes
    !
    version 15.4
    no service timestamps log datetime msec
    no service timestamps debug datetime msec
    no service password-encryption
    !
    hostname R1
    !
    interface Serial0/1/0
     description Circuit ID BCB123450001
     ip address 209.165.200.229 255.255.255.248
     clock rate 2000000
    ```

    - IP address and subnet mask on interface `Serial0/1/0` are incorrect. To correct it:

    ```bash
    R1(config)# interface serial0/1/0
    R1(config-if)# ip address 209.165.200.225 255.255.255.252
    ```

3. **Display the routing table by filtering to start the output at the word `Gateway`.**

    ```bash
    R1# show ip route | begin Gate
    Gateway of last resort is 209.165.200.226 to network 0.0.0.0

         172.16.0.0/16 is variably subnetted, 4 subnets, 2 masks
    C       172.16.20.0/25 is directly connected, GigabitEthernet0/0/0
    L       172.16.20.1/32 is directly connected, GigabitEthernet0/0/0
    C       172.16.20.128/25 is directly connected, GigabitEthernet0/0/1
    L       172.16.20.129/32 is directly connected, GigabitEthernet0/0/1
         209.165.200.0/24 is variably subnetted, 2 subnets, 2 masks
    C       209.165.200.224/30 is directly connected, Serial0/1/0
    L       209.165.200.225/32 is directly connected, Serial0/1/0
    S*   0.0.0.0/0 [1/0] via 209.165.200.226
    ```

    - What is the Gateway of last resort address?  
    **Answer:** `209.165.200.226`

4. **Display interface information and filter for Description or connected.**

    ```bash
    R1# show interface | include Desc|conn
    ```

    - What is the Circuit ID displayed from your output?  
    **Answer:** `BCB123450001`

    ```bash
    R1# show interface | include Desc|conn
    GigabitEthernet0/0/0 is up, line protocol is up (connected)
      Description: Connection to SW1
    GigabitEthernet0/0/1 is up, line protocol is up (connected)
      Description: Connection to SW2
    Serial0/1/0 is up, line protocol is up (connected)
      Description: Circuit ID BCB123450001
    R1#
    ```

5. **Display specific interface information for G0/0/0 by filtering for duplex.**

    ```bash
    R1# show interfaces gigabitEthernet 0/0/0 | include duplex
    ```

    - What is the duplex setting, speed, and media type?  
    **Answer:** Full-duplex, 100Mb/s, media type is RJ45.

#### Step 2: Verify Connectivity

- PC1 and PC2 should be able to ping each other and the Dual Stack Server. If not, verify the status of the interfaces and the IP address assignments.

---

### Part 2: Verify IPv6 Directly Connected Networks

#### Step 1: Verify IPv6 addresses and port status on R2.

1. **Check the status of the configured interfaces:**

    ```bash
    R2# show ipv6 int brief
    ```

    - What is the status of the configured interfaces?  
    **Answer:** Interfaces GigabitEthernet0/0/0, GigabitEthernet0/0/1, and Serial0/1/1 are all up/up. Serial0/1/0 is administratively down/down.

    ```bash
    R2# show ipv6 interface brief
    GigabitEthernet0/0/0       [up/up]
        FE80::2
        2001:DB8:C0DE:12::1
    GigabitEthernet0/0/1       [up/up]
        FE80::2
        2001:DB8:C0DE:14::1
    Serial0/1/0                [administratively down/down]
        unassigned
    Serial0/1/1                [up/up]
        FE80::2D0:BCFF:FE32:7C24
        2001:DB8:C0DE:11::1
    Vlan1                      [administratively down/down]
        unassigned
    R2#
    ```

2. **Refer to the Addressing Table and make any corrections to addressing as necessary.**

    - To change an IPv6 address on an interface:

    ```bash
    R2(config)# int g0/0/1
    R2(config-if)# no ipv6 address 2001:db8:c0de:14::1/64
    R2(config-if)# ipv6 address 2001:db8:c0de:13::1/64
    R2(config-if)# exit
    ```

    - To remove an incorrect IPv6 address on `Serial0/1/1` and configure the correct one:

    ```bash
    R2(config)# int s0/1/1
    R2(config-if)# no ipv6 address 2001:DB8:C0DE:11::1/64
    R2(config-if)# ipv6 address 2001:db8:c0de:11::1/64
    ```

3. **Display the IPv6 routing table:**

    ```bash
    R2# show ipv6 route
    ```

    - What are the connected routes for IPv6?  
    **Answer:** The connected routes are:
    - **Connected IPv6 Routes:**
        - `2001:DB8:C0DE:11::/64` (via Serial0/1/1)
        - `2001:DB8:C0DE:12::/64` (via GigabitEthernet0/0/0)
        - `2001:DB8:C0DE:13::/64` (via GigabitEthernet0/0/1)
        - Link-local `FE80::/10` (via Null0)

    The Gateway for `::/0` (default route) is reachable via `Serial0/1/1` directly.

4. **Display all IPv6 addressing configured on interfaces by filtering the output of the running-config:**

    ```bash
    R2# show run | include ipv6|interface
    ```

    - What are the IPv6 addresses configured on R2 interfaces?  
    **Answer:**
    - On `GigabitEthernet0/0/0`:  
      - `FE80::2` (Link-local)
      - `2001:DB8:C0DE:12::1/64` (Global address)
    - On `GigabitEthernet0/0/1`:  
      - `FE80::2` (Link-local)
      - `2001:DB8:C0DE:13::1/64` (Global address)
    - On `Serial0/1/1`:  
      - `FE80::2D0:BCFF:FE32:7C24` (Link-local)
      - `2001:DB8:C0DE:11::1/64` (Global address)

    - **How many addresses are configured on each Gigabit interface?**
      - **Answer:** There are **2 IPv6 addresses** configured on each Gigabit interface: one global address (`/64`) and one link-local address.

#### Step 2: Verify Connectivity

- **PC3 and PC4 should be able to ping each other and the Dual Stack Server.** If they cannot, verify the interface status and IPv6 address assignments.
  
    If they are unable to communicate:
    - Ensure that the interfaces on R2 are correctly configured and not in the `administratively down` state.
    - Ensure that `PC3` and `PC4` are correctly configured with their respective IPv6 addresses and default gateways as per the Addressing Table.
    - Confirm the routing configuration on R2 and make sure the `ipv6 unicast-routing` is enabled.

---

## Conclusion

This lab exercise is designed to verify both IPv4 and IPv6 connectivity across routers and workstations. It emphasizes checking interface status, verifying IP configurations, and troubleshooting any issues related to network connectivity. By following these instructions, you should be able to successfully verify the addressing and connectivity between the networks on both IPv4 and IPv6.

---
**End of Instructor Version**