# Virtual Port Channel (vPC)

<br>

## Tasks

>1. Configure the mgmt0 interfaces with the denoted IPs.
>2. Configure vPC domain 1 with the denoted IPs and priorities.
>3. Configure the vPC Peer-Link using Port-Channel 1000.
>4. Configure vPC 10 toward SWITCH and allow only VLAN 10.
>5. Configure vPC 20 toward SERVER in VLAN 20.
>6. Enable Bridge Assurance and set the port types correctly.
>7. Create SVIs and enable HSRP Active/Active forwarding on both VLANs.
>8. Test HSRP Active/Active forwarding.

<br>

---

<br>

## Solutions

<br>

### 1. Configure mgmt0 Interfaces

<br>

```text
# NX1
configure terminal
interface mgmt0
  vrf member management
  ip address 192.168.255.1/24
  end

```

<br>

```text
# NX2
configure terminal
interface mgmt0
  vrf member management
  ip address 192.168.255.2/24
  end

```

<br>

#### Verification
```text
# NX1
ping 192.168.255.2 vrf management count 5

```

<br>

### 2. Configure vPC Domain 1

<br>

```text
# NX1
configure terminal
feature vpc

vpc domain 1
  role priority 1
  peer-keepalive destination 192.168.255.2 source 192.168.255.1
  end

```

<br>

```text
# NX2
configure terminal
feature vpc

vpc domain 1
  role priority 2
  peer-keepalive destination 192.168.255.1 source 192.168.255.2
  end

```

<br>

### 3. Configure the vPC Peer-Link

<br>

```text
# NX1 & NX2
configure terminal
feature lacp

default interface Ethernet1/1
default interface Ethernet1/2

no interface port-channel1000

interface Ethernet1/1 - 2
  shutdown
  switchport
  channel-group 1000 mode active
  no shutdown

interface port-channel1000
  switchport mode trunk
  vpc peer-link
  no shutdown
  end

```

<br>

#### Verification
```text
# NX1
terminal length 0
watch show vpc

```

<br>

### 4. Configure vPC 10 Toward the IOS-SWITCH

<br>

```text
# NX1 & NX2
configure terminal
vlan 10
  name IOS-SWITCH

default interface Ethernet1/3

no interface port-channel10

interface Ethernet1/3
  shutdown
  switchport
  channel-group 10 mode active
  no shutdown

interface port-channel10
  switchport mode trunk
  switchport trunk allowed vlan 10
  vpc 10
  no shutdown
  end

```

<br>

#### Verification
```text
# NX1
terminal length 0
watch show vpc

```

<br>

>##### NOTE: You may need to hit the [return] key a few times on the `IOS-SWITCH`.

```text
# IOS-SWITCH



show etherchannel summary

```

<br>

### 5. Configure vPC 20 Toward SERVER

<br>

```text
# NX1 & NX2
configure terminal
vlan 20
  name SERVER

default interface Ethernet1/4

no interface port-channel 20

interface Ethernet1/4
  shutdown
  switchport
  channel-group 20 mode active
  no shutdown

interface port-channel20
  switchport mode access
  switchport access vlan 20
  vpc 20
  no shutdown
  end

```

<br>

>##### NOTE: You may need to hit the [return] key a few times on the `SERVER`

```text
# SERVER



show etherchannel summary

```

<br>

### 6. Enable Bridge Assurance and Set Port Types

> **NOTE:** Port-Channel10 should use spanning-tree port type normal, and Port-Channel20 should use spanning-tree port type edge.

<br>

```text
# NX1 & NX2
configure terminal
spanning-tree bridge assurance
end

```

<br>

#### Verify ports are in a Bridge Assurance Inconsistent (BKN*) state
```text
# NX1 & NX2
show spanning-tree interface port-channel 10
show spanning-tree interface port-channel 20

```

<br>

```text
# NX1 & NX2
configure terminal
interface port-channel10
  spanning-tree port type normal

interface port-channel20
  spanning-tree port type edge
  end

```


<br>

#### Verify ports are operational again
```text
# NX1 & NX2
show spanning-tree interface port-channel 10
show spanning-tree interface port-channel 20

```

<br>

### 7. Configure SVIs and HSRP Active/Active Forwarding

<br>

```text
# NX1
configure terminal
feature interface-vlan
feature hsrp

vpc domain 1
  peer-gateway

interface Vlan10
  no shutdown
  ip address 192.168.10.2/24
  hsrp version 2
  hsrp 10
    authentication text pa55w0rd
    preempt
    priority 200
    ip 192.168.10.1

interface Vlan20
  no shutdown
  ip address 192.168.20.2/24
  hsrp version 2
  hsrp 20
    authentication text pa55w0rd
    preempt
    priority 200
    ip 192.168.20.1
  end

```

<br>

```text
# NX2
configure terminal
feature interface-vlan
feature hsrp

vpc domain 1
  peer-gateway

interface Vlan10
  no shutdown
  ip address 192.168.10.3/24
  hsrp version 2
  hsrp 10
    authentication text pa55w0rd
    preempt
    priority 150
    ip 192.168.10.1

interface Vlan20
  no shutdown
  ip address 192.168.20.3/24
  hsrp version 2
  hsrp 20
    authentication text pa55w0rd
    preempt
    priority 150
    ip 192.168.20.1
  end

```

<br>

### 8. Test HSRP Active/Active Forwarding

<br>

##### Verify that `NX` is HSRP Active
```text
# NX1 & NX2
show mac address-table dynamic vlan 10
show mac address-table dynamic vlan 20
show hsrp group 10
show hsrp group 20

```

<br>

##### Start a continuous ping from `IOS-SWITCH` to `SERVER`
```text
# IOS-SWITCH
ping 192.168.20.10 repeat 10000

```

<br>

##### Make sure `NX1` is forwarding the packets
```text
# NX1 & NX2
watch show interface Ethernet1/4 counters

```

<br>

##### Lower `NX1`'s HSRP priority to 100
```text
# NX1
configure terminal
interface Vlan10
  hsrp 10
    priority 100

interface Vlan20
  hsrp 20
    priority 100

```

<br>

##### Verify `NX1` is not HSRP Standby
```text
# NX1 & NX2
show hsrp

```

<br>

##### Verify `NX1` is still the one forwarding packets
```text
# NX1 & NX2
watch show interface Ethernet1/4 counters

```

<br>

---

<br>

## Troubleshooting

| Device      | Check                       | Command                                       | Purpose                                                            |              |                                                 |
| ----------- | --------------------------- | --------------------------------------------- | ------------------------------------------------------------------ | ------------ | ----------------------------------------------- |
| `NX1/NX2`       | Management Interface Status | `show ip interface brief vrf management`      | Ensure mgmt0 is in the correct VRF and has the correct IP address. |              |                                                 |
| `NX1/NX2`       | Management Reachability     | `ping 192.168.255.2 vrf management`           | Ensure NX1 can reach the mgmt0 interface of NX2.                   |              |                                                 |
| `NX1/NX2` | vPC Domain Configuration    | `show running-config                          | section 'feature                                                   | vpc domain'` | Ensure the vPC domain configuration is correct. |
| `NX1/NX2` | vPC Peer Status             | `show vpc brief`                              | Ensure the peer adjacency was formed successfully.                 |              |                                                 |
| `NX1/NX2` | vPC Consistency             | `show vpc consistency-parameters vpc 10`      | Ensure the vPC configuration is synchronized on both switches.     |              |                                                 |
| `NX1/NX2` | Port-Channel Status         | `show port-channel summary`                   | Ensure member ports are in the `(P)` bundled state.                |              |                                                 |
| `NX1/NX2` | VLAN Status                 | `show vlan id 10`                             | Ensure VLAN 10 exists and the correct ports are assigned.          |              |                                                 |
| `NX1/NX2` | Bridge Assurance            | `show spanning-tree summary`                  | Ensure Bridge Assurance is enabled.                                |              |                                                 |
| `NX1/NX2` | STP Interface State         | `show spanning-tree interface Port-channel10` | Ensure the interface is in the forwarding state.                   |              |                                                 |
| `NX1/NX2` | SVI Status                  | `show interface Vlan10`                       | Ensure the SVI is up and has the correct IP address.               |              |                                                 |
| `NX1/NX2` | HSRP Status                 | `show hsrp`                                   | Ensure authentication matches and the correct switch is active.    |              |                                                 |
| `NX1/NX2` | MAC Learning                | `show mac address-table dynamic`              | Ensure MAC addresses are being learned on both port-channels.      |              |                                                 |
| `NX1/NX2` | ARP Resolution              | `show ip arp`                                 | Ensure ARP is resolving correctly for both end systems.            |              |                                                 |
