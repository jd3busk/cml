# VLAN Trunking Protocol (VTP)

<br>

## Tasks

>1. Statically configure all links as 802.1Q trunks.
>2. Configure CSW as VTPv2 Server.
>3. Configure DSW as VTPv2 Transparent.
>4. Configure ASW as VTPv2 Client.
>5. Configure the domain name and password as `LAB`.
>6. Ensure VLANs 10, 20, and 30 exist on all switches.

<br>

---

<br>

## Solutions

<br>

### 1. Statically configure all links as 802.1Q trunks.

<br>

```text
# CSW
configure terminal
interface GigabitEthernet0/0
 no shutdown
 switchport
 switchport trunk encapsulation dot1q
 switchport mode trunk
end

````

<br>

```text
# DSW
configure terminal
interface range GigabitEthernet0/0 - 1
 no shutdown
 switchport
 switchport trunk encapsulation dot1q
 switchport mode trunk
end

```

<br>

```text
# ASW
configure terminal
interface GigabitEthernet0/0
 no shutdown
 switchport
 switchport trunk encapsulation dot1q
 switchport mode trunk
end

```

<br>

### 2. Configure CSW as VTPv2 Server.

<br>

```text
# CSW
configure terminal
vtp version 2
vtp mode server
end

```

<br>

### 3. Configure DSW as VTPv2 Transparent.

<br>

```text
# DSW
configure terminal
vtp version 2
vtp mode transparent
end

```

<br>

### 4. Configure ASW as VTPv2 Client.

<br>

```text
# ASW
configure terminal
vtp version 2
vtp mode client
end

```

<br>

### 5. Configure the domain name and password as `LAB`.

<br>

```text
# CSW, DSW & ASW
configure terminal
vtp domain LAB
vtp password LAB
end

```

<br>

### 6. Ensure VLANs 10, 20, and 30 exist on all switches.

<br>

```text
# CSW
configure terminal
vlan 10, 20, 30
end

```

<br>

> **NOTE:** **CSW** will advertise VLAN changes to **VTP clients**, but **DSW** is in **transparent mode**, so it only forwards those updates and does not apply them locally. VLANs must therefore also be created manually on **DSW**.

<br>

```text
# DSW
configure terminal
vlan 10
vlan 20
vlan 30
end

```

<br>

---

<br>

## Verification

<br>

> **Note 1:** Confirm that the VTP version, mode, domain, and password are correct on all switches.

> **Note 2:** Confirm that VLANs 10, 20, and 30 are present and that the VTP revision reflects synchronization where applicable.

<br>

```text
# CSW, DSW & ASW
show vtp status | include version running|Domain
show vtp password
show vlan brief
show vtp status | include Revision

```

<br>

---

<br>

## Troubleshooting

| Device        | Check                     | Command                                        | Purpose                                               |
| ------------- | ------------------------- | ---------------------------------------------- | ----------------------------------------------------- |
| `CSW/DSW/ASW` | Interface Status          | `show interfaces status`                       | Ensure the ports are not disabled.                    |
| `CSW/DSW/ASW` | Specific Interface Status | `show interface GigabitEthernet0/1`            | Verify the interface is up and operational.           |
| `CSW/DSW/ASW` | Switchport Mode           | `show interface GigabitEthernet0/1 switchport` | Ensure the port is in the correct operational mode.   |
| `CSW/DSW/ASW` | VTP Status                | `show vtp status`                              | Verify the VTP domain, version, and mode.             |
| `CSW/DSW/ASW` | VTP Password              | `show vtp password`                            | Ensure that the VTP password matches on all switches. |
| `CSW/DSW/ASW` | VLAN Presence             | `show vlan brief`                              | Verify that VLANs 10, 20, and 30 exist.               |
| `CSW/DSW/ASW` | Specific VLAN Check       | `show vlan id 10`                              | Confirm that the appropriate VLAN exists.             |
