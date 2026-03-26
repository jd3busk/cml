# Lab 3: EtherChannels

<br>

## Tasks

>1. Configure PAgP between SW1 and SW2, with SW1 actively trying to form the EtherChannel.
>2. Configure LACP between SW1 and SW3, with SW1 actively trying to form the EtherChannel.
>3. Configure forced EtherChannel bonding between SW2 and SW3.
>4. Configure all EtherChannels as Layer 2 trunks.
>5. Change the load-balancing method to destination MAC address on all switches.
>6. Verify and troubleshoot EtherChannel, trunking, and spanning-tree operation.

<br>

---

<br>

## Solutions

<br>

### 1. Configure PAgP between SW1 and SW2

<br>

```text
# SW1
configure terminal
interface range GigabitEthernet0/0 - 1
 channel-group 12 mode desirable
 
````

<br>

```text
# SW2
configure terminal
interface range GigabitEthernet0/0 - 1
 channel-group 12 mode auto

```

<br>

### 2. Configure Port-channel 12 as a trunk

<br>

```text
# SW1
interface Port-channel 12
 switchport trunk encapsulation dot1q
 switchport mode trunk
 end

```

<br>

```text
# SW2
interface Port-channel 12
 switchport trunk encapsulation dot1q
 switchport mode trunk
 end

```

<br>

### 3. Configure LACP between SW1 and SW3

<br>

```text
# SW1
configure terminal
interface range GigabitEthernet0/2 - 3
 channel-group 13 mode active

```

<br>

```text
# SW3
configure terminal
interface range GigabitEthernet0/0 - 1
 channel-group 13 mode passive

```

<br>

### 4. Configure forced EtherChannel between SW2 and SW3

<br>

```text
# SW2
configure terminal
interface range GigabitEthernet0/2 - 3
 channel-group 23 mode on

interface Port-channel 23
 switchport trunk encapsulation dot1q
 switchport mode trunk
 end

```

<br>

```text
# SW3
configure terminal
interface range GigabitEthernet0/2 - 3
 channel-group 23 mode on

interface Port-channel 23
 switchport trunk encapsulation dot1q
 switchport mode trunk
 end

```

<br>

### 5. Configure Port-channel 13 as a trunk

<br>

```text
# SW1
interface Port-channel 13
 switchport trunk encapsulation dot1q
 switchport mode trunk
 end

```

<br>

```text
# SW3
interface Port-channel 13
 switchport trunk encapsulation dot1q
 switchport mode trunk
 end

```

<br>

### 6. Change load-balancing to destination MAC

> **NOTE:** Apply this on all three switches.

<br>

```text
# SW1
configure terminal
port-channel load-balance dst-mac
end

```

<br>

```text
# SW2
configure terminal
port-channel load-balance dst-mac
end

```

<br>

```text
# SW3
configure terminal
port-channel load-balance dst-mac
end

```

<br>

---

<br>

## Verification

<br>

> **Note 1:** Verify that EtherChannel member interfaces are bundled and show `(P)` in the summary output.

> **Note 2:** Verify that the port-channels are trunking properly, spanning-tree is forwarding, and load-balancing is based on destination MAC.

<br>

```text
# SW1
show etherchannel summary
show interfaces trunk
show spanning-tree interface Port-channel12
show spanning-tree interface Port-channel13
show etherchannel load-balance

```

<br>

```text
# SW2
show etherchannel summary
show etherchannel 12 detail
show etherchannel 23 detail
show interfaces trunk
show spanning-tree interface Port-channel12
show spanning-tree interface Port-channel23
show etherchannel load-balance

```

<br>

```text
# SW3
show etherchannel summary
show etherchannel 13 detail
show etherchannel 23 detail
show interfaces trunk
show spanning-tree interface Port-channel13
show spanning-tree interface Port-channel23
show etherchannel load-balance

```

<br>

---

<br>

## Troubleshooting

| Device          | Check                 | Command                                        | Purpose                                                                       |
| --------------- | --------------------- | ---------------------------------------------- | ----------------------------------------------------------------------------- |
| `SW1/SW2/SW3` | Interface Status      | `show interfaces status`                       | Ensure that the ports are not disabled.                                       |
| `SW1/SW2/SW3` | Interface Details     | `show interface GigabitEthernet0/1`            | Ensure that the ports are not disabled.                                       |
| `SW1/SW2/SW3` | Switchport Mode       | `show interface GigabitEthernet0/1 switchport` | Ensure the port is in the correct operational mode.                           |
| `SW1/SW2/SW3` | EtherChannel Summary  | `show etherchannel summary`                    | Ensure the port-channel member statuses show `(P)`.                           |
| `SW1/SW2/SW3` | EtherChannel Detail   | `show etherchannel 12 detail`                  | Ensure local and remote ports are enabled and not suspended.                  |
| `SW1/SW2/SW3` | Trunk Status          | `show interfaces trunk`                        | Ensure that the port-channels are working as Layer 2 trunks.                  |
| `SW1/SW2/SW3` | STP State             | `show spanning-tree interface Port-channel12`  | Ensure that spanning-tree is in the FWD state on the port-channel.            |
| `SW1/SW2/SW3` | Load-Balancing Method | `show etherchannel load-balance`               | Ensure that the load-balancing algorithm is based on destination MAC address. |
