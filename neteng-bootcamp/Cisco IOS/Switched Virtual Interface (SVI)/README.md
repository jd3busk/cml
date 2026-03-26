# Switched Virtual Interfaces (SVI)

<br>

## Tasks

>1. Create VLAN 10 on `SW1`
>2. Assign `PC-1` to VLAN 10
>3. Create VLAN 20 on `SW1`
>4. Assign `PC-2` to VLAN 20
>5. Enable IP routing on `SW1`
>6. Configure SVIs for VLANs 10 & 20 on `SW1`

<br>

---

<br>

## Solutions

<br>

### 1. Create VLAN 10 on SW1

<br>

```text
# On SW1
configure terminal
vlan 10
  exit
```
<br>



### 2. Assign `PC-1` to VLAN 10

<br>

```text
# On SW1
configure terminal
interface GigabitEthernet0/1
  switchport
  switchport mode access
  switchport access vlan 10
end
```
<br>



### 3. Create VLAN 20 on `SW1`

<br>

```text
# On SW1
configure terminal
vlan 20
  exit
```
<br>



### 4. Assign `PC-2` to VLAN 20

<br>

```text
 # On SW1
configure terminal
interface GigabitEthernet0/2
  switchport
  switchport mode access
  switchport access vlan 20
end
```
<br>



### 5. Enable IP routing on `SW1`

<br>

```text
# On SW1
configure terminal
ip routing
end
```
<br>



### 6. Configure SVIs for VLANs 10 & 20 on `SW1`

<br>

```text
# On SW1
configure terminal

interface Vlan10
  ip address 192.168.10.1 255.255.255.0
  no shutdown

interface Vlan20
  ip address 192.168.20.1 255.255.255.0
  no shutdown
end
```

<br>

---

<br>

## Verification

<br>

>**Note1:** PC-1 credentials are cisco:cisco

>**Note2:** After port configurations, SW1 will take 45 seconds for Spanning-Tree to transition to the FWD state.

<br>

```text
# On PC-1
ping -c 5 192.168.20.10
```
<br>

---

<br>

## Troubleshooting

| Device | Check | Command | Purpose |
|--------|-------|---------|---------|
| `SW1` | Interface status | `show interfaces status` | Verify interfaces are up and not disabled |
| `SW1` | Interface status | `show interface GigabitEthernet0/1` | Verify a specific interface is up and not disabled |
| `SW1` | VLANs | `show vlan brief` | Verify required VLANs exist |
| `SW1` | VLANs | `show vlan id 10` | Verify a specific VLAN exists |
| `SW1` | Switchport mode | `show interface GigabitEthernet0/1 switchport` | Verify switchport mode and VLAN assignment |
| `SW1` | Spanning tree state | `show spanning-tree vlan 10` | Verify the interface is forwarding |
| `SW1` | MAC learning | `show mac address-table vlan 10` | Verify MAC learning on the correct VLAN |
| `SW1` | SVI status | `show ip interface brief` | Verify VLAN interfaces are up with correct IPs |
| `SW1` | RIB state | `show ip rib database` | Verify the RIB is operational |
| `SW1` | Routing table | `show ip route` | Verify VLAN networks appear in the routing table |
| `SW1` | ARP | `show ip arp` | Verify ARP resolution for connected hosts |
| `SW1` | Connectivity test | `ping 192.168.10.10 source Vlan20` | Verify return traffic through the default gateway |