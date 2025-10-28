# 🧷 11.1.10 – Implement Port Security (Sticky MACs)  
**Cisco Packet Tracer Lab Guide**

## 📘 Overview
This lab demonstrates how to **implement and verify switch port security** using the **Sticky MAC Address** feature on a Cisco switch.  
Port security helps prevent unauthorized devices (like rogue laptops) from connecting to the network by controlling which MAC addresses can access specific switch ports.

---

## 🎯 Objectives
1. Configure port security on switch **S1**.
2. Enable **Sticky MAC learning** on ports **Fa0/1** and **Fa0/2**.
3. Restrict each port to **one allowed device**.
4. Test and verify the configuration using connected PCs.
5. Observe what happens when an unauthorized device connects.

---

## 🧩 Network Topology
| Device | Interface | IP Address    | Subnet Mask       |
|:-------:|:----------|:--------------|:------------------|
| S1      | VLAN 1    | 10.10.10.2    | 255.255.255.0     |
| PC1     | NIC       | 10.10.10.10   | 255.255.255.0     |
| PC2     | NIC       | 10.10.10.11   | 255.255.255.0     |
| Rogue Laptop | NIC | 10.10.10.12   | 255.255.255.0     |

---

## ⚙️ Configuration Steps

### 1. Access S1 CLI
```bash
enable
configure terminal
```

### 2. Configure Port Security on Fa0/1 and Fa0/2
```bash
interface range f0/1 - 2
 switchport mode access
 switchport port-security
 switchport port-security maximum 1
 switchport port-security mac-address sticky
 switchport port-security violation restrict
exit
```

> 💡 **Explanation:**
> - `mode access` → sets the port as a non-trunk access port.  
> - `port-security` → enables security features on the interface.  
> - `maximum 1` → limits the port to one allowed device.  
> - `mac-address sticky` → dynamically learns the MAC and saves it.  
> - `violation restrict` → drops unauthorized traffic but keeps port active.

---

### 3. Disable All Unused Ports
```bash
interface range f0/3 - 24 , g0/1 - 2
 shutdown
exit
```

---

### 4. Generate Network Traffic
To make the switch learn the connected devices’ MAC addresses, send pings:

From **PC1**:
```bash
ping 10.10.10.11
```

From **PC2**:
```bash
ping 10.10.10.10
```

---

### 5. Verify Sticky MAC Learning
Check that MACs have been learned and marked as “SecureSticky”:
```bash
show port-security address
```

Expected output:
```
Vlan    Mac Address       Type             Ports
----    -----------       ----             -----
1       00E0.F9A3.1C2D    SecureSticky     Fa0/1
1       00E0.F9A3.2B4C    SecureSticky     Fa0/2
```

---

### 6. Save Configuration
```bash
copy running-config startup-config
```

Verify sticky MAC entries in the running config:
```bash
show run | begin interface
```

You should see:
```
switchport port-security mac-address sticky 00E0.F9A3.1C2D
```

---

## 🧪 Testing and Verification

### ✅ Normal Operation
- PC1 ↔ PC2 communication works normally.  
- Each port learns one MAC address dynamically.

### 🚫 Violation Test
1. Disconnect **PC2** and connect **Rogue Laptop** to **Fa0/2**.
2. Try to ping **PC1** — it should **fail**.
3. Check port security violation count:
   ```bash
   show port-security interface f0/2
   ```

Expected:
```
Security Violation Count: 4
Security Action: Restrict
```

4. Reconnect **PC2** — it will work again because its MAC is authorized.

---

## 📚 Key Concepts

| Term | Meaning |
|------|----------|
| **Port Security** | Limits which MAC addresses can send traffic through a switch port. |
| **Sticky MAC** | Automatically learns and saves the MAC address of the first connected device. |
| **Violation Mode: Restrict** | Drops unauthorized frames, logs violation, but keeps port up. |
| **Maximum 1** | Only one device can connect per port. |

---

## 🧠 Why Sticky MACs Matter
Sticky MACs provide a balance between **security** and **convenience**:
- You don’t have to manually type every MAC address.
- Once learned, they persist in the configuration.
- Unauthorized devices can’t connect even if they plug into a valid port.

This protects your network from **rogue access**, **MAC flooding**, and **unauthorized device connections**.

---

## 🏁 Conclusion
By enabling port security with sticky MAC addresses, you ensure that each switch port only accepts traffic from known, trusted devices.  
This lab demonstrates a foundational **layer 2 security technique** that’s essential in real-world LAN environments.

---

### 🧾 Commands Summary
```bash
interface range f0/1 - 2
 switchport mode access
 switchport port-security
 switchport port-security maximum 1
 switchport port-security mac-address sticky
 switchport port-security violation restrict
exit

interface range f0/3 - 24 , g0/1 - 2
 shutdown

copy running-config startup-config
show port-security
show port-security address
show port-security interface f0/1
```

---

**Author:** John Lemar L. Gonzales 
**Lab:** 11.1.10 – Implement Port Security (Sticky MACs)
