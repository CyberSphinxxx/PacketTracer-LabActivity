# Packet Tracer – Configure SSH (Instructions & Answer Key)

## Addressing Table

| Device | Interface | IP Address  | Subnet Mask     |
|--------|-----------|-------------|-----------------|
| S1     | VLAN 1    | 10.10.10.2  | 255.255.255.0   |
| PC1    | NIC       | 10.10.10.10 | 255.255.255.0   |

---

## Objectives
- **Part 1:** Secure Passwords  
- **Part 2:** Encrypt Communications  
- **Part 3:** Verify SSH Implementation  

---

## Background
SSH should replace Telnet for management connections. Telnet uses insecure plain text communications. SSH provides security for remote connections by providing strong encryption of all transmitted data between devices.  

In this activity, you will secure a remote switch with password encryption and SSH.

---

## Instructions

### Part 1: Secure Passwords

1. Using the command prompt on **PC1**, Telnet to **S1**.  
   - The user EXEC and privileged EXEC password is **cisco**.

2. Save the current configuration so that any mistakes can be reversed by toggling the power for S1.

```bash
S1# copy running-config startup-config
Destination filename [startup-config]? 
Building configuration...
[OK]
S1#
```

3. Show the current configuration and note that the passwords are in plain text. Enter the command that encrypts plain text passwords.

```bash
S1# show running-config
...
enable password cisco
...
line vty 0 4
 password cisco
 login
line vty 5 15
 password cisco
 login
...
```

Enable encryption:

```bash
S1(config)# service password-encryption
```

4. Verify that the passwords are now encrypted.

```bash
S1# show running-config
...
enable password 7 0822455D0A16
line vty 0 4
 password 7 0822455D0A16
 login
line vty 5 15
 password 7 0822455D0A16
 login
...
```

---

### Part 2: Encrypt Communications

#### Step 1: Set the IP domain name and generate secure keys.

a. Configure the domain name:

```bash
S1(config)# ip domain-name netacad.pka
```

b. Generate RSA keys using a **1024-bit key length**:

```bash
S1(config)# crypto key generate rsa
How many bits in the modulus [512]: 1024
% Generating 1024 bit RSA keys, keys will be non-exportable...[OK]
```

#### Step 2: Create an SSH user and configure the VTY lines.

a. Create an administrator user:

```bash
S1(config)# username administrator secret cisco
```

b. Configure VTY lines for SSH-only access:

```bash
S1(config)# line vty 0 15
S1(config-line)# transport input ssh
S1(config-line)# login local
S1(config-line)# no password cisco
```

---

### Part 3: Verify SSH Implementation

1. Exit the Telnet session and attempt to log back in using Telnet. The attempt should **fail**.

```bash
C:\> telnet 10.10.10.2
Trying 10.10.10.2 ...Open
[Connection to 10.10.10.2 closed by foreign host]
```

2. Attempt to log in using SSH:

```bash
C:\> ssh -l administrator 10.10.10.2
Password: 
S1> enable
Password: 
S1#
```

3. Upon successful login, enter privileged EXEC mode and save the configuration.

```bash
S1# copy running-config startup-config
Destination filename [startup-config]? 
Building configuration...
[OK]
S1#
```

---

## Answer Key

```bash
config terminal
service password-encryption
ip domain-name netacad.pka
crypto key generate rsa
1024
username administrator secret cisco
line vty 0 15
 login local
 transport input ssh
 no password cisco
```
