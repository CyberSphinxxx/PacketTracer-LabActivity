# 1.4.7 Packet Tracer – Configure Router Interfaces (Answer Guide)

## Addressing Table
> Attach your addressing table image here, or fill in the optional table below.

| Device | Interface | IPv4 Address | Subnet Mask | IPv6 Address/Prefix | Default Gateway |
|---|---|---|---|---|---|
| R1 | G0/0 | 172.16.20.1/25 | 255.255.255.128 | — | — |
| R1 | G0/1 | 172.16.20.129/25 | 255.255.255.128 | — | — |
| R2 | G0/0 | — | — | 2001:db8:c0de:12::1/64 | — |
| R2 | G0/1 | — | — | 2001:db8:c0de:13::1/64 | — |
| PC1 | NIC | 172.16.20.10 /25 | *(mask)* | — | 172.16.20.1 |
| PC2 | NIC | 172.16.20.138 /25 | *(mask)* | — | 172.16.20.129 |
| PC3 | NIC | — | — | 2001:db8:c0de:12::a/64 | fe80::2 |
| PC4 | NIC | — | — | 2001:db8:c0de:13::a/64 | fe80::2 |

---

## Objectives
- **Part 1:** Configure IPv4 Addressing and Verify Connectivity  
- **Part 2:** Configure IPv6 Addressing and Verify Connectivity  

---

## Background
Routers **R1** and **R2** each have two LANs. Your task is to configure the appropriate addressing on each device and verify connectivity between the LANs.

> **Device Access:**  
> - User EXEC password: `cisco`  
> - Privileged EXEC password: `class`  

---

## Part 1 — Configure IPv4 Addressing and Verify Connectivity

### Step 1: Assign IPv4 addresses to R1 and LAN devices
Referring to the Addressing Table, configure IP addressing for **R1 LAN interfaces**, **PC1**, and **PC2**.  
The serial interface has already been configured.

```plaintext
R1>enable
Password: 
R1#configure terminal

R1(config)#interface g0/0
R1(config-if)#ip address 172.16.20.1 255.255.255.128
R1(config-if)#no shutdown

R1(config)#interface g0/1
R1(config-if)#ip address 172.16.20.129 255.255.255.128
R1(config-if)#no shutdown
```

📷 *Attach PC1 and PC2 IP configuration screenshots here.*

> **Tip:** On each PC, set the **default gateway** to the router interface IP on the same subnet.

---

### Step 2: Verify IPv4 connectivity
PC1 and PC2 should be able to ping each other and the Dual Stack Server.

```console
C:\> ping 172.16.20.138

Pinging 172.16.20.138 with 32 bytes of data:
Request timed out.
Reply from 172.16.20.138: bytes=32 time<1ms TTL=127
Reply from 172.16.20.138: bytes=32 time<1ms TTL=127
Reply from 172.16.20.138: bytes=32 time=13ms TTL=127

Ping statistics for 172.16.20.138:
    Packets: Sent = 4, Received = 3, Lost = 1 (25% loss),
    Approximate round trip times in milli-seconds:
        Minimum = 0ms, Maximum = 13ms, Average = 4ms
```

```console
C:\> ping 64.100.1.10

Pinging 64.100.1.10 with 32 bytes of data:
Request timed out.
Reply from 64.100.1.10: bytes=32 time=2ms TTL=126
Reply from 64.100.1.10: bytes=32 time=13ms TTL=126
Reply from 64.100.1.10: bytes=32 time=1ms TTL=126

Ping statistics for 64.100.1.10:
    Packets: Sent = 4, Received = 3, Lost = 1 (25% loss),
    Approximate round trip times in milli-seconds:
        Minimum = 1ms, Maximum = 13ms, Average = 5ms
```

---

## Part 2 — Configure IPv6 Addressing and Verify Connectivity

### Step 1: Assign IPv6 addresses to R2 and LAN devices
Referring to the Addressing Table, configure IP addressing for **R2 LAN interfaces**, **PC3**, and **PC4**.  
The serial interface has already been configured.

```plaintext
R2>enable
Password: 
R2#configure terminal

R2(config)#interface g0/0
R2(config-if)#ipv6 address 2001:db8:c0de:12::1/64
R2(config-if)#no shutdown
R2(config-if)#exit

R2(config)#interface g0/1
R2(config-if)#ipv6 address 2001:db8:c0de:13::1/64
R2(config-if)#no shutdown
```

📷 *Attach PC3 and PC4 IPv6 configuration screenshots here.*

> **Tip:** Ensure IPv6 is enabled on PCs and that the **default gateway** is the router’s IPv6 address on the local link.

---

### Step 2: Verify IPv6 connectivity
PC3 and PC4 should be able to ping each other and the Dual Stack Server.

```console
C:\> ping 2001:db8:c0de:12::a

Pinging 2001:db8:c0de:12::a with 32 bytes of data:
Reply from 2001:DB8:C0DE:12::A: bytes=32 time=13ms TTL=127
Reply from 2001:DB8:C0DE:12::A: bytes=32 time<1ms TTL=127
Reply from 2001:DB8:C0DE:12::A: bytes=32 time<1ms TTL=127
Reply from 2001:DB8:C0DE:12::A: bytes=32 time<1ms TTL=127

Ping statistics for 2001:DB8:C0DE:12::A:
    Packets: Sent = 4, Received = 4, Lost = 0 (0% loss),
    Approximate round trip times in milli-seconds:
        Minimum = 0ms, Maximum = 13ms, Average = 3ms
```

```console
C:\> ping 2001:db8:100:1::a

Pinging 2001:db8:100:1::a with 32 bytes of data:
Reply from 2001:DB8:100:1::A: bytes=32 time=15ms TTL=126
Reply from 2001:DB8:100:1::A: bytes=32 time=7ms TTL=126
Reply from 2001:DB8:100:1::A: bytes=32 time=13ms TTL=126
Reply from 2001:DB8:100:1::A: bytes=32 time=4ms TTL=126

Ping statistics for 2001:DB8:100:1::A:
    Packets: Sent = 4, Received = 4, Lost = 0 (0% loss),
    Approximate round trip times in milli-seconds:
        Minimum = 4ms, Maximum = 15ms, Average = 9ms
```

---

## Quick Troubleshooting
- **No reply / timeouts:** Check `no shutdown` on interfaces and verify correct IP/mask/prefix.
- **PC cannot reach server:** Confirm **default gateway** on the PC matches the router interface in that subnet.
- **IPv6 issues:** Ensure IPv6 is enabled on devices and prefixes match on the correct LAN.
- **Routing between subnets:** Verify serial/WAN links are up and any necessary routing is configured (static or dynamic) if required by the activity.

---

✅ **End of Activity**