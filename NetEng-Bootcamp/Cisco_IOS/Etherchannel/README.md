# Lab 3: EtherChannels

<br>

## Tasks

>1. Configure a PAgP Etherchannel between `SW1` and `SW2`
>   - `SW1` should be actively trying to form the EtherChannel.
>2. Configure an LACP Etherchannel between `SW1` and `SW3`.
>   - `SW1` should be actively trying to form the EtherChannel.
>3. Configure a Static Layer 3 EtherChannel between `SW2` and `SW3` 
>   - `SW2` = 192.168.23.2/24
>   - `SW3` = 192.168.23.3/24
>5. Change the load-balancing method to destination MAC address on all switches.
>6. Verify that the Etherchannels are operational.

<br>

---

<br>

## Solutions

<br>

### 1. Configure a PAgP Etherchannel between `SW1` and `SW2`

<br>

```text
# SW1
configure terminal
default interface GigabitEthernet0/0
default interface GigabitEthernet0/1

interface range GigabitEthernet0/0 - 1
  shutdown
  switchport
  channel-group 12 mode desirable
  no shutdown

interface Port-channel 12
  switchport trunk encapsulation dot1q
  switchport mode trunk
  no shutdown
  end

```
<br>

```text
# SW2
configure terminal
default interface GigabitEthernet0/0
default interface GigabitEthernet0/1

interface range GigabitEthernet0/0 - 1
  shutdown
  switchport
  channel-group 12 mode auto
  no shutdown

interface Port-channel 12
  switchport trunk encapsulation dot1q
  switchport mode trunk
  no shutdown
  end

```
<br>

### 2. Configure an LACP Etherchannel between `SW1` and `SW3`

<br>

```text
# SW1
configure terminal
default interface GigabitEthernet0/2
default interface GigabitEthernet0/3

interface range GigabitEthernet0/2 - 3
  shutdown
  switchport
  channel-group 13 mode active
  no shutdown

interface Port-channel 13
  switchport trunk encapsulation dot1q
  switchport mode trunk
  no shutdown
  end

```

<br>

```text
# SW3
configure terminal
default interface GigabitEthernet0/0
default interface GigabitEthernet0/1

interface range GigabitEthernet0/0 - 1
  shutdown
  switchport
  channel-group 13 mode passive
  no shutdown

interface Port-channel 13
  switchport trunk encapsulation dot1q
  switchport mode trunk
  no shutdown
  end

```

<br>

### 3. Configure a Static Layer 3 EtherChannel between `SW2` and `SW3`

<br>

```text
# SW2
configure terminal
default interface GigabitEthernet0/2
default interface GigabitEthernet0/3

interface range GigabitEthernet0/2 - 3
  shutdown
  no switchport
  channel-group 23 mode on
  no shutdown

interface Port-channel 23
  ip address 192.168.23.2 255.255.255.0
  no shutdown
  end

```

<br>

```text
# SW3
configure terminal
default interface GigabitEthernet0/2
default interface GigabitEthernet0/3

interface range GigabitEthernet0/2 - 3
  shutdown
  no switchport
  channel-group 23 mode on
  no shutdown

interface Port-channel 23
  ip address 192.168.23.3 255.255.255.0
  no shutdown
  end

```

<br>

### 4. Change load-balancing to destination MAC

> **NOTE:** This configuration only applies on the local switch. So it should be configured on both sides.

<br>

```text
# SW1, SW2 & SW3
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
terminal length 0
show etherchannel summary
show interfaces trunk
show spanning-tree interface Port-channel12
show spanning-tree interface Port-channel13
show etherchannel load-balance

```

<br>

```text
# SW2
terminal length 0
show etherchannel summary
show etherchannel 12 detail
show etherchannel 23 detail
show interfaces trunk
show spanning-tree interface Port-channel12
show spanning-tree interface Port-channel23
show etherchannel load-balance
ping 192.168.23.3

```

<br>

```text
# SW3
terminal length 0
show etherchannel summary
show etherchannel 13 detail
show etherchannel 23 detail
show interfaces trunk
show spanning-tree interface Port-channel13
show spanning-tree interface Port-channel23
show etherchannel load-balance
ping 192.168.23.2

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
