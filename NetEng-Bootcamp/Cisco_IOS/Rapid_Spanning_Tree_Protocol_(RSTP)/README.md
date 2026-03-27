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

#### Verify Rapid PVST is now operational
```text
# DSW1, DSW2, ASW1 & ASW2
show spanning-tree summary | include ^Switch is in

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

#### Verify DSW1 is root for VLAN 10
```text
# DSW1, DSW2 & ASW1
show spanning-tree bridge
show spanning-tree vlan 10 | section ID

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

#### Verify DSW2 is root for VLAN 20
```text
# DSW1, DSW2 & ASW2
show spanning-tree bridge
show spanning-tree vlan 20 | section ID

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

#### Verify DSW1 is root for VLAN 30
```text
# DSW1, DSW2 & ASW1
show spanning-tree bridge
show spanning-tree vlan 30 | section ID

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

#### Verify DSW2 is root for VLAN 40
```text
# DSW1, DSW2 & ASW2
show spanning-tree bridge
show spanning-tree vlan 40 | section ID

```

<br>

### 6. Ensure that VLAN 10’s hello timer is set to 4 seconds.

<br>

#### Verify VLAN 10's hello timer is 2
```text
# ASW1
show spanning-tree bridge
show spanning-tree vlan 10 | section ID
show spanning-tree vlan 10 detail | section Spanning Tree

```

<br>

#### Configure VLAN 10's hello timer for 4 seconds

<br>

```text
# DSW1
configure terminal
spanning-tree vlan 10 hello-time 4
end

```

<br>

#### Verify VLAN 10's new hello timer propagated
```text
# ASW1
show spanning-tree bridge
show spanning-tree vlan 10 | section ID
show spanning-tree vlan 10 detail | section Spanning Tree

```

<br>

### 7. From DSW2, ensure ASW2's GigabitEthernet0/2 is used to reach the root bridge only for VLAN 20.

<br>

#### Verify ASW2's GigabitEthernet0/1 is preferred for VLAN 20
```text
# ASW2
show spanning-tree vlan 20
show spanning-tree vlan 20 detail | include VLAN0020|port id

```

<br>

```text
# DSW2
configure terminal
interface GigabitEthernet0/2
 spanning-tree vlan 20 port-priority 240
end

```

<br>

#### Verify ASW2's GigabitEthernet0/2 is now preferred for VLAN 20
```text
# ASW2
show spanning-tree vlan 20
show spanning-tree vlan 20 detail | include VLAN0020|port id

```

<br>

### 8. From ASW2, ensure ASW2's GigabitEthernet0/2 is used to reach the root bridge for VLAN 40.

<br>

#### Verify ASW2's GigabitEthernet0/1 is preferred for VLAN 40
```text
# ASW2
show spanning-tree vlan 40
show spanning-tree vlan 40 detail | include VLAN0040|Port path cost

```

<br>

#### Now change ASW2's path cost to prefer GigabitEthernet0/2
```text
# ASW2
configure terminal
interface GigabitEthernet0/2
 spanning-tree vlan 40 cost 1
end

```

<br>

#### Verify ASW2's GigabitEthernet0/2 is now preferred for VLAN 40
```text
# ASW2
show spanning-tree vlan 40
show spanning-tree vlan 40 detail | include VLAN0040|Port path cost

```

<br>

### 9. On DSW1 & DSW2, only allow VLANs 10 & 30 toward ASW1

<br>

```text
# DSW1 & DSW2
configure terminal
interface GigabitEthernet0/1
 switchport trunk allowed vlan 10,30
end

```

<br>

#### Verify that only VLANs 10 & 30 are allowed toward ASW2
```text
# DSW1 & DSW2
show spanning-tree interface GigabitEthernet0/1
show interfaces trunk | include Port|Gi0/1

```

<br>

### 10. On DSW1 & DSW2, only allow VLANs 20 & 40 toward ASW2

<br>

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

#### Verify that only VLANs 20 & 40 are allowed toward ASW2
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

### 11. Protect DSW1 & 2 from receiving any superior BPDUs from either access switch.

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

<br>

#### Verify that Root Guard is enabled
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

#### Test DSW1 & DSW2's Root Guard configuration by setting ASW1 as VLAN 10's root
```text
# ASW1
configure terminal
spanning-tree vlan 10 priority 0
end

```

<br>

#### Verify that Root Guard was triggered on DSW1 and DSW2 for VLAN 10
```text
# DSW1 & DSW2
show spanning-tree interface GigabitEthernet0/1
show spanning-tree inconsistentports

```

<br>

### 12. Ensure ASW1's Edge Ports immediately transitions to the forwarding state.

<br>

```text
# ASW1
configure terminal
interface range GigabitEthernet0/2 - 3
 spanning-tree portfast edge
 no shutdown
end

```
<br>

#### Verify that ASW1's Gi0/2 and Gi0/3 ports transitioned to FWD state
```text
# ASW1
show spanning-tree interface GigabitEthernet0/2
show spanning-tree interface GigabitEthernet0/3

```

<br>

### 13. Prevent ROGUE access switches from connecting to the network

<br>

```text
# ASW1
configure terminal
interface range GigabitEthernet0/2 - 3
 spanning-tree bpduguard enable
 no shutdown
end

```
<br>

#### Verify that BPDUGuard triggered on ASW1's Gi0/3 port
```text
# ASW1
show interface status err-disabled
show spanning-tree interface GigabitEthernet0/2
show spanning-tree interface GigabitEthernet0/3

```

<br>

---

<br>

## Troubleshooting

| Device               | Check                         | Command                                                  | Purpose                                                                                              |
| -------------------- | ----------------------------- | -------------------------------------------------------- | ---------------------------------------------------------------------------------------------------- |
| `DSW1 / DSW2`        | STP Bridge Status             | `show spanning-tree bridge`                              | Confirm spanning-tree is active and review bridge information during root and timer troubleshooting. |
| `DSW1 / DSW2`        | Root Bridge                   | `show spanning-tree root`                                | Confirm the correct switch is acting as the root bridge.                                             |
| `DSW1 / DSW2 / ASW1` | VLAN Forwarding State         | `show spanning-tree vlan 10`                             | Verify VLAN 10 root placement and forwarding/root-port behavior.                                     |
| `ASW1`               | Hello Timer Verification      | `show spanning-tree vlan 10 detail`                      | Confirm the updated hello timer has propagated from the root bridge.                                 |
| `ASW2`               | VLAN 20 Path Selection        | `show spanning-tree vlan 20 detail`                      | Review detailed STP information to confirm the preferred path toward the root for VLAN 20.           |
| `ASW2`               | VLAN 40 Path Cost             | `show spanning-tree vlan 40 detail`                      | Review detailed STP information to confirm the preferred path toward the root for VLAN 40.           |
| `DSW1 / DSW2`        | Trunk VLAN Allow List         | `show interfaces trunk`                                  | Verify the correct trunk links are active and carrying the intended VLANs.                           |
| `ASW1 / ASW2`        | Port Status                   | `show interfaces status`                                 | Check whether interfaces are connected, disabled, or err-disabled.                                   |
| `ASW1 / ASW2`        | Interface Status              | `show interface GigabitEthernet0/1`                      | Inspect physical and line protocol status on a switch uplink.                                        |
| `ASW1 / ASW2`        | Switchport Mode               | `show interface GigabitEthernet0/1 switchport`           | Confirm the uplink is operating in the expected switchport mode.                                     |
| `DSW1 / DSW2`        | Root Guard Interface State    | `show spanning-tree interface GigabitEthernet0/1`        | Verify Root Guard behavior on the access-switch-facing interface.                                    |
| `DSW1 / DSW2`        | Root Guard Inconsistent Ports | `show spanning-tree inconsistentports`                   | Identify ports blocked because they received superior BPDUs.                                         |
| `ASW1`               | Edge Port State               | `show spanning-tree interface GigabitEthernet0/2`        | Confirm the PortFast edge port transitioned properly and is forwarding.                              |
| `ASW1`               | Edge Port BPDU Check          | `show spanning-tree interface GigabitEthernet0/2 detail` | Verify the PC-facing edge port has not received BPDUs.                                               |
| `ASW1`               | BPDU Guard Err-Disable Check  | `show interface status err-disabled`                     | Confirm whether BPDU Guard has shut down an edge port after rogue-switch detection.                  |
