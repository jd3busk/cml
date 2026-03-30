# RSTP

<br>

## Tasks

> 1. Enable Rapid PVST+ on all switches
> 2. Set DSW1 as root bridge for VLAN 10 and DSW2 as backup
> 3. Set DSW2 as root bridge for VLAN 20 and DSW1 as backup
> 4. Set DSW1 as root bridge for VLAN 30 and DSW2 as backup
> 5. Set DSW2 as root bridge for VLAN 40 and DSW1 as backup
> 6. Set VLAN 10 hello timer to 4 seconds
> 7. Prefer ASW2 Gi0/2 toward the root for VLAN 20
> 8. Prefer ASW2 Gi0/2 toward the root for VLAN 40
> 9. Allow only VLANs 10 and 30 toward ASW1
> 10. Allow only VLANs 20 and 40 toward ASW2
> 11. Protect DSW1 and DSW2 from superior BPDUs
> 12. Enable PortFast on access ports
> 13. Enable BPDU Guard on access ports

<br>

---

<br>

## Solutions

<br>

### 1. Enable Rapid PVST+

<br>

#### Configure STP mode
```text
# DSW1, DSW2, ASW1 & ASW2
configure terminal
spanning-tree mode rapid-pvst
end

````

<br>

#### Verify STP mode

```text
# DSW1, DSW2, ASW1 & ASW2
show spanning-tree summary | include ^Switch is in

```

<br>

### 2. VLAN 10 Root and Backup

<br>

#### Set DSW1 as root

```text
# DSW1
configure terminal
spanning-tree vlan 10 root primary
end

```

<br>

#### Set DSW2 as backup

```text
# DSW2
configure terminal
spanning-tree vlan 10 root secondary
end

```

<br>

#### Verify VLAN 10 root

```text
# DSW1, DSW2 & ASW1
show spanning-tree bridge
show spanning-tree vlan 10 | section ID

```

<br>

### 3. VLAN 20 Root and Backup

<br>

#### Set DSW2 as root

```text
# DSW2
configure terminal
spanning-tree vlan 20 root primary
end

```

<br>

#### Set DSW1 as backup

```text
# DSW1
configure terminal
spanning-tree vlan 20 root secondary
end

```

<br>

#### Verify VLAN 20 root

```text
# DSW1, DSW2 & ASW2
show spanning-tree bridge
show spanning-tree vlan 20 | section ID

```

<br>

### 4. VLAN 30 Root and Backup

<br>

#### Set DSW1 as root

```text
# DSW1
configure terminal
spanning-tree vlan 30 priority 4096
end

```

<br>

#### Set DSW2 as backup

```text
# DSW2
configure terminal
spanning-tree vlan 30 priority 8192
end

```

<br>

#### Verify VLAN 30 root

```text
# DSW1, DSW2 & ASW1
show spanning-tree bridge
show spanning-tree vlan 30 | section ID

```

<br>

### 5. VLAN 40 Root and Backup

<br>

#### Set DSW2 as root

```text
# DSW2
configure terminal
spanning-tree vlan 40 priority 4096
end

```

<br>

#### Set DSW1 as backup

```text
# DSW1
configure terminal
spanning-tree vlan 40 priority 8192
end

```

<br>

#### Verify VLAN 40 root

```text
# DSW1, DSW2 & ASW2
show spanning-tree bridge
show spanning-tree vlan 40 | section ID

```

<br>

### 6. VLAN 10 Hello Timer

<br>

#### Check current timer

```text
# ASW1
show spanning-tree bridge
show spanning-tree vlan 10 | section ID
show spanning-tree vlan 10 detail | section Spanning Tree

```

<br>

#### Set hello timer

```text
# DSW1
configure terminal
spanning-tree vlan 10 hello-time 4
end

```

<br>

#### Verify updated timer

```text
# ASW1
show spanning-tree bridge
show spanning-tree vlan 10 | section ID
show spanning-tree vlan 10 detail | section Spanning Tree

```

<br>

### 7. VLAN 20 Preferred Uplink

<br>

#### Check current root port

```text
# ASW2
show spanning-tree vlan 20
show spanning-tree vlan 20 detail | include VLAN0020|port id

```

<br>

#### Lower port priority on DSW2 Gi0/2

```text
# DSW2
configure terminal
interface GigabitEthernet0/2
 spanning-tree vlan 20 port-priority 240
end

```

<br>

#### Verify preferred uplink

```text
# ASW2
show spanning-tree vlan 20
show spanning-tree vlan 20 detail | include VLAN0020|port id

```

<br>

### 8. VLAN 40 Preferred Uplink

<br>

#### Check current path cost

```text
# ASW2
show spanning-tree vlan 40
show spanning-tree vlan 40 detail | include VLAN0040|Port path cost

```

<br>

#### Lower path cost on ASW2 Gi0/2

```text
# ASW2
configure terminal
interface GigabitEthernet0/2
 spanning-tree vlan 40 cost 1
end

```

<br>

#### Verify preferred uplink

```text
# ASW2
show spanning-tree vlan 40
show spanning-tree vlan 40 detail | include VLAN0040|Port path cost

```

<br>

### 9. VLAN Allow List Toward ASW1

<br>

#### Check current trunk VLANs

```text
# DSW1 & DSW2
show spanning-tree interface GigabitEthernet0/1
show interfaces trunk | include Port|Gi0/1

```

<br>

#### Allow VLANs 10 and 30

```text
# DSW1 & DSW2
configure terminal
interface GigabitEthernet0/1
 switchport trunk allowed vlan 10,30
end

```

<br>

#### Verify trunk VLANs

```text
# DSW1 & DSW2
show spanning-tree interface GigabitEthernet0/1
show interfaces trunk | include Port|Gi0/1

```

<br>

### 10. VLAN Allow List Toward ASW2

<br>

#### Check current trunk VLANs

```text
# DSW1
show spanning-tree interface GigabitEthernet0/2
show interfaces trunk | include Port|Gi0/2

```

<br>

```text
# DSW2
show spanning-tree interface GigabitEthernet0/2
show spanning-tree interface GigabitEthernet0/3
show interfaces trunk | include Port|Gi0/2|Gi0/3

```

<br>

#### Allow VLANs 20 and 40

```text
# DSW1
configure terminal
interface GigabitEthernet0/2
 switchport trunk allowed vlan 20,40
end

```

<br>

```text
# DSW2
configure terminal
interface range GigabitEthernet0/2 - 3
 switchport trunk allowed vlan 20,40
end

```

<br>

#### Verify trunk VLANs

```text
# DSW1
show spanning-tree interface GigabitEthernet0/2
show interfaces trunk | include Port|Gi0/2

```

<br>

```text
# DSW2
show spanning-tree interface GigabitEthernet0/2
show spanning-tree interface GigabitEthernet0/3
show interfaces trunk | include Port|Gi0/2|Gi0/3

```

<br>

### 11. Root Guard on Distribution Switches

<br>

#### Enable Root Guard on DSW1

```text
# DSW1
configure terminal
interface range GigabitEthernet0/1 - 2
 spanning-tree guard root
end

```

<br>

#### Enable Root Guard on DSW2

```text
# DSW2
configure terminal
interface range GigabitEthernet0/1 - 3
 spanning-tree guard root
end

```

<br>

#### Verify Root Guard

```text
# DSW1
show spanning-tree interface Gi0/1 detail | include VLAN|Root guard
show spanning-tree interface Gi0/2 detail | include VLAN|Root guard

```

<br>

```text
# DSW2
show spanning-tree interface Gi0/1 detail | include VLAN|Root guard
show spanning-tree interface Gi0/2 detail | include VLAN|Root guard
show spanning-tree interface Gi0/3 detail | include VLAN|Root guard

```

<br>

#### Trigger Root Guard

```text
# ASW1
configure terminal
spanning-tree vlan 30 priority 0
end

```

<br>

#### Verify inconsistent ports

```text
# DSW1 & DSW2
show spanning-tree interface GigabitEthernet0/1
show spanning-tree inconsistentports

```

<br>

### 12. PortFast on Access Ports

<br>

#### Verify forwarding state transition is slow

```text
# ASW1 & ASW2
configure terminal
interface GigabitEthernet0/3
 shutdown
 no shutdown
 end

show spanning-tree interface GigabitEthernet0/3

```

<br>


#### Enable PortFast

```text
# ASW1 & ASW2
configure terminal
interface GigabitEthernet0/3
 shutdown
 spanning-tree portfast edge
 no shutdown
end

```

<br>

#### Verify forwarding state transition is faster

```text
# ASW1 & ASW2
show spanning-tree interface GigabitEthernet0/3

```

<br>

### 13. BPDU Guard on Access Ports

<br>

#### Enable BPDU Guard

```text
# ASW1 & ASW2
configure terminal
interface GigabitEthernet0/3
 spanning-tree bpduguard enable
 no shutdown
end

```

<br>

#### Verify err-disabled state

```text
# ASW1 & ASW2
show interface status err-disabled
show spanning-tree interface GigabitEthernet0/3

```

<br>

---

<br>

## Troubleshooting

| Device               | Check                         | Command                                                  | Purpose                                          |
| -------------------- | ----------------------------- | -------------------------------------------------------- | ------------------------------------------------ |
| `DSW1 / DSW2`        | STP Bridge Status             | `show spanning-tree bridge`                              | Review bridge ID, root ID, and STP operation     |
| `DSW1 / DSW2`        | Root Bridge                   | `show spanning-tree root`                                | Confirm the active root per VLAN                 |
| `DSW1 / DSW2 / ASW1` | VLAN 10 STP State             | `show spanning-tree vlan 10`                             | Verify VLAN 10 root and port roles               |
| `ASW1`               | VLAN 10 Hello Timer           | `show spanning-tree vlan 10 detail`                      | Confirm hello timer propagation                  |
| `ASW2`               | VLAN 20 Path Selection        | `show spanning-tree vlan 20 detail`                      | Check root-port selection for VLAN 20            |
| `ASW2`               | VLAN 40 Path Selection        | `show spanning-tree vlan 40 detail`                      | Check root-port selection for VLAN 40            |
| `DSW1 / DSW2`        | Trunk VLAN Allow List         | `show interfaces trunk`                                  | Verify allowed VLANs on trunks                   |
| `ASW1 / ASW2`        | Interface Status              | `show interfaces status`                                 | Check connected, disabled, or err-disabled ports |
| `ASW1 / ASW2`        | Uplink Interface State        | `show interface GigabitEthernet0/1`                      | Review physical and line protocol state          |
| `ASW1 / ASW2`        | Uplink Switchport Mode        | `show interface GigabitEthernet0/1 switchport`           | Confirm switchport mode and trunking state       |
| `DSW1 / DSW2`        | Root Guard State              | `show spanning-tree interface GigabitEthernet0/1`        | Verify Root Guard on access-facing trunks        |
| `DSW1 / DSW2`        | Root Guard Inconsistent Ports | `show spanning-tree inconsistentports`                   | Identify ports blocked by superior BPDUs         |
| `ASW1 / ASW2`        | Access Port State             | `show spanning-tree interface GigabitEthernet0/3`        | Confirm PortFast forwarding state                |
| `ASW1 / ASW2`        | Access Port Detail            | `show spanning-tree interface GigabitEthernet0/3 detail` | Check BPDU activity and edge-port status         |
| `ASW1 / ASW2`        | BPDU Guard Err-Disable        | `show interface status err-disabled`                     | Confirm BPDU Guard shutdown after rogue BPDU     |