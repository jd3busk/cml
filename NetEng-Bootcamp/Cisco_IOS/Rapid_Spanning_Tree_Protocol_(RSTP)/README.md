# RSTP

<br>

## Tasks

> 1. Enable Rapid PVST+ on all switches
> 2. Ensure DSW1 is the root bridge for VLAN 10, and DSW2 is its backup. (Use ‘spanning-tree root’)
> 3. Ensure DSW2 is the root bridge for VLAN 20, and DSW1 is its backup. (Use ‘spanning-tree root’)
> 4. Ensure DSW1 is the root bridge for VLAN 30, and DSW2 its backup. (Use ‘spanning-tree priority)
> 5. Ensure SW4 is the root bridge for VLAN 40, and SW3 is its backup.
> 6. Ensure that VLAN 10’s hello timer is set to 4 seconds.
> 7. From DSW2, ensure ASW2's GigabitEthernet0/2 is used to reach the root bridge only for VLAN 20.
> 8. From ASW2, ensure ASW2's GigabitEthernet0/2 is used to reach the root bridge for VLAN 40.
> 9. Protect DSW1 & 2 from receiving any superior BPDUs from either access switch.
> 10. From DSW1 & 2, only allow VLANs 10 & 30 toward ASW1 and VLANs 20 & 40 toward ASW2.
> 11. Ensure PC-1's switchport immediately and safely transitions to the forwarding state.

<br>

---

<br>

## Solutions

<br>

### 1. Enable Rapid PVST+ on all switches

<br>

```text
# DSW1, DSW2, ASW1 & ASW2
configure terminal
spanning-tree mode rapid-pvst
end

```

<br>

### 2. Ensure DSW1 is the root bridge for VLAN 10, and DSW2 is its backup. (Use ‘spanning-tree root’)

<br>

```text
# DSW1
configure terminal
spanning-tree vlan 10 root primary
end

```

<br>

```text
# DSW2
configure terminal
spanning-tree vlan 10 root secondary
end

```

<br>

### 3. Ensure DSW2 is the root bridge for VLAN 20, and DSW1 is its backup. (Use ‘spanning-tree root’)

<br>

```text
# DSW2
configure terminal
spanning-tree vlan 20 root primary
end

```

<br>

```text
# DSW1
configure terminal
spanning-tree vlan 20 root secondary
end

```

<br>

### 4. Ensure DSW1 is the root bridge for VLAN 30, and DSW2 its backup. (Use ‘spanning-tree priority)

<br>

```text
# DSW1
configure terminal
spanning-tree vlan 30 priority 4096
end

```

<br>

```text
# DSW2
configure terminal
spanning-tree vlan 30 priority 8192
end

```

<br>

### 5. Ensure SW4 is the root bridge for VLAN 40, and SW3 is its backup.

<br>

```text
# DSW2
configure terminal
spanning-tree vlan 40 priority 4096
end

```

<br>

```text
# DSW1
configure terminal
spanning-tree vlan 40 priority 8192
end

```

<br>

### 6. Ensure that VLAN 10’s hello timer is set to 4 seconds.

<br>

```text
# DSW1
configure terminal
spanning-tree vlan 10 hello-time 4
end

```

<br>

### 7. From DSW2, ensure ASW2's GigabitEthernet0/2 is used to reach the root bridge only for VLAN 20.

<br>

```text
# DSW2
configure terminal
interface GigabitEthernet0/2
 spanning-tree vlan 20 port-priority 240
end

```

<br>

### 8. From ASW2, ensure ASW2's GigabitEthernet0/2 is used to reach the root bridge for VLAN 40.

<br>

```text
# ASW2
configure terminal
interface GigabitEthernet0/2
 spanning-tree vlan 40 cost 1
end

```

<br>

### 9. Protect DSW1 & 2 from receiving any superior BPDUs from either access switch.

<br>

```text
# DSW1
configure terminal
interface range GigabitEthernet0/1 - 2
 spanning-tree guard root
end

```

<br>

```text
# DSW2
configure terminal
interface range GigabitEthernet0/1 - 3
 spanning-tree guard root
end

```

<br>

```text
# ASW1
configure terminal
spanning-tree vlan 1 priority 0
end

```

<br>

### 10. From DSW1 & 2, only allow VLANs 10 & 30 toward ASW1 and VLANs 20 & 40 toward ASW2.

<br>

```text
# DSW1
configure terminal
interface GigabitEthernet0/1
 switchport trunk allowed vlan 10,20

interface GigabitEthernet0/2
 switchport trunk allowed vlan 20,40
end

```

<br>

```text
# DSW2
configure terminal
interface GigabitEthernet0/1
 switchport trunk allowed vlan 10,20

interface range GigabitEthernet0/2 - 3
 switchport trunk allowed vlan 20,40
end

```

<br>

### 11. Ensure PC-1's switchport immediately and safely transitions to the forwarding state.

<br>

```text
# ASW1
configure terminal
interface GigabitEthernet0/2
 shutdown
 spanning-tree portfast edge
 spanning-tree bpduguard enable
 no shutdown
end

```

<br>

---

<br>

## Verification

<br>

> **Note 1:** Verify root bridge placement, timers, and per-VLAN path selection after completing the configuration.

> **Note 2:** Use the interface-specific spanning-tree checks to confirm Root Guard and PortFast behavior.

<br>

```text
# DSW1
show spanning-tree bridge protocol
show spanning-tree root
show spanning-tree vlan 10
show spanning-tree vlan 10 | section Root ID
show spanning-tree interface GigabitEthernet0/2
show spanning-tree inconsistentports

```

<br>

```text
# DSW2
show spanning-tree bridge protocol
show spanning-tree root
show spanning-tree detail | section VLAN0020
show spanning-tree interface GigabitEthernet0/2

```

<br>

```text
# ASW1 / ASW2
show interfaces status
show interface GigabitEthernet0/1
show interface GigabitEthernet0/1 switchport
show spanning-tree interface GigabitEthernet0/2
show spanning-tree interface GigabitEthernet0/2 detail

```

<br>

---

<br>

## Troubleshooting

| Device        | Check                      | Command                                                  | Purpose                                                                         |                                                                  |
| ------------- | -------------------------- | -------------------------------------------------------- | ------------------------------------------------------------------------------- | ---------------------------------------------------------------- |
| `ASW1 / ASW2` | Port Status                | `show interfaces status`                                 | Ensure that the ports are not disabled.                                         |                                                                  |
| `ASW1 / ASW2` | Interface Status           | `show interface GigabitEthernet0/1`                      | Ensure that the ports are not disabled.                                         |                                                                  |
| `ASW1 / ASW2` | Switchport Mode            | `show interface GigabitEthernet0/1 switchport`           | Ensure the port is in the correct ‘operational mode’.                           |                                                                  |
| `DSW1 / DSW2` | STP Protocol               | `show spanning-tree bridge protocol`                     | Ensure that the switch is running in RSTP mode for all VLANs.                   |                                                                  |
| `DSW1 / DSW2` | Root Bridge                | `show spanning-tree root`                                | Ensure that the proper switch is ROOT (won’t have any Root Ports).              |                                                                  |
| `DSW1 / DSW2` | VLAN 10 Forwarding         | `show spanning-tree vlan 10`                             | Ensure spanning-tree is forwarding on the correct ports.                        |                                                                  |
| `DSW1`        | Hello Timer Propagation    | `show spanning-tree vlan 10 \| section Root ID`                                                                | Ensure the Root Bridge has told everyone of the new Hello Timer. |
| `DSW2`        | VLAN 20 Path Selection     | `show spanning-tree detail \| section VLAN0020`                                                               | Review the “Port path cost” and “Designated port id”.            |
| `DSW1 / DSW2` | Root Guard State           | `show spanning-tree inconsistentports`                   | Ensure Rootguard is triggered by a superior BPDUs received on downstream ports. |                                                                  |
| `DSW1 / DSW2` | Root Guard Interface Check | `show spanning-tree interface GigabitEthernet0/2`        | Ensure Rootguard is triggered by a superior BPDUs received on downstream ports. |                                                                  |
| `ASW1`        | PortFast Edge Type         | `show spanning-tree interface GigabitEthernet0/2`        | Ensure that the portfast enabled port shows as a “P2p Edge” type.               |                                                                  |
| `ASW1`        | BPDU Receipt Check         | `show spanning-tree interface GigabitEthernet0/2 detail` | Ensure that the PC-facing edge port has not received any BPDUs.                 |                                                                  |
