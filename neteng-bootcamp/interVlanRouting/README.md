# Lab 1: Inter-VLAN Routing

## Tasks

1. Create VLAN 10 on **SW1**.
2. Assign **PC-1**'s switchport to VLAN 10.
3. Create VLAN 20 on **SW1**.
4. Assign **PC-2**'s switchport to VLAN 20.
5. Configure **R1**'s switchport as an 802.1Q trunk.
6. Configure **R1** to perform inter-VLAN routing.
7. Disable **R1**'s switchport and move routing to **SW1**.

---

## Step 1: Create VLAN 10 on SW1 and assign PC-1

### SW1

```text
configure terminal
vlan 10
exit

interface GigabitEthernet0/1
 switchport
 switchport mode access
 switchport access vlan 10
end
````

---

## Step 2: Create VLAN 20 on SW1 and assign PC-2

### SW1

```text
configure terminal
vlan 20
exit

interface GigabitEthernet0/2
 switchport
 switchport mode access
 switchport access vlan 20
end
```

---

## Step 3: Configure R1's switchport as an 802.1Q trunk

### SW1

```text
configure terminal
interface GigabitEthernet0/0
 switchport
 switchport trunk encapsulation dot1q
 switchport mode trunk
end
```

---

## Step 4: Configure R1 for inter-VLAN routing

### R1

```text
configure terminal
interface GigabitEthernet0/0
 no shutdown

interface GigabitEthernet0/0.10
 encapsulation dot1q 10
 ip address 192.168.10.1 255.255.255.0
 no shutdown

interface GigabitEthernet0/0.20
 encapsulation dot1q 20
 ip address 192.168.20.1 255.255.255.0
 no shutdown
end
```

### PC-1

```text
ping 192.168.20.10
```

---

## Step 5: Disable R1's switchport and move routing to SW1

### SW1

```text
configure terminal
interface GigabitEthernet0/0
 shutdown

interface Vlan10
 ip address 192.168.10.1 255.255.255.0
 no shutdown

interface Vlan20
 ip address 192.168.20.1 255.255.255.0
 no shutdown

ip routing
end
```

### PC-1

```text
ping 192.168.20.10
```

---

# Troubleshooting

## Switch Troubleshooting

### Check interface status

```text
show interfaces status
```

or

```text
show interface GigabitEthernet0/1
```

Ensure that the ports are not disabled.

### Check VLANs

```text
show vlan brief
```

or

```text
show vlan id 10
```

Ensure that the appropriate VLANs exist.

### Check switchport mode

```text
show interface GigabitEthernet0/1 switchport
```

Ensure the port is in the correct operational mode.

### Check spanning tree

```text
show spanning-tree vlan 10
```

Ensure the ports are in the spanning-tree forwarding state.

### Check MAC learning

```text
show mac address-table vlan 10
```

Ensure that MAC addresses are being learned on the ports.

---

## L3 Switch / Router Commands

### Verify interface IP status

```text
show ip interface brief
```

Ensure the port is enabled and has the correct IP address.

### Verify RIB state

```text
show ip rib database
```

Ensure the default RIB table is in the UP state.

### Verify routing table

```text
show ip route
```

Ensure that all relevant networks appear in the routing table.

### Verify ARP

```text
show ip arp
```

Ensure that ARP is resolving for the relevant IP addresses.

### Test host gateway reachability

```text
ping 192.168.10.10 source GigabitEthernet0/0.20
```

or

```text
ping 192.168.10.10 source Vlan20
```

Ensure the host has a default gateway by sourcing a ping from a different IP network on the same router.
