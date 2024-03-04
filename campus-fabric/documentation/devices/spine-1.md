# spine-1

## Table of Contents

- [Management](#management)
  - [Management Interfaces](#management-interfaces)
  - [IP Name Servers](#ip-name-servers)
  - [NTP](#ntp)
  - [Management API HTTP](#management-api-http)
- [Authentication](#authentication)
  - [Local Users](#local-users)
  - [AAA Authorization](#aaa-authorization)
- [MLAG](#mlag)
  - [MLAG Summary](#mlag-summary)
  - [MLAG Device Configuration](#mlag-device-configuration)
- [Spanning Tree](#spanning-tree)
  - [Spanning Tree Summary](#spanning-tree-summary)
  - [Spanning Tree Device Configuration](#spanning-tree-device-configuration)
- [Internal VLAN Allocation Policy](#internal-vlan-allocation-policy)
  - [Internal VLAN Allocation Policy Summary](#internal-vlan-allocation-policy-summary)
  - [Internal VLAN Allocation Policy Device Configuration](#internal-vlan-allocation-policy-device-configuration)
- [VLANs](#vlans)
  - [VLANs Summary](#vlans-summary)
  - [VLANs Device Configuration](#vlans-device-configuration)
- [Interfaces](#interfaces)
  - [Ethernet Interfaces](#ethernet-interfaces)
  - [Port-Channel Interfaces](#port-channel-interfaces)
  - [Loopback Interfaces](#loopback-interfaces)
  - [VLAN Interfaces](#vlan-interfaces)
- [Routing](#routing)
  - [Service Routing Protocols Model](#service-routing-protocols-model)
  - [Virtual Router MAC Address](#virtual-router-mac-address)
  - [IP Routing](#ip-routing)
  - [IPv6 Routing](#ipv6-routing)
  - [Static Routes](#static-routes)
  - [Router BGP](#router-bgp)
- [Multicast](#multicast)
  - [IP IGMP Snooping](#ip-igmp-snooping)
- [Filters](#filters)
  - [Prefix-lists](#prefix-lists)
  - [Route-maps](#route-maps)
- [VRF Instances](#vrf-instances)
  - [VRF Instances Summary](#vrf-instances-summary)
  - [VRF Instances Device Configuration](#vrf-instances-device-configuration)

## Management

### Management Interfaces

#### Management Interfaces Summary

##### IPv4

| Management Interface | Description | Type | VRF | IP Address | Gateway |
| -------------------- | ----------- | ---- | --- | ---------- | ------- |
| Management0 | oob_management | oob | MGMT | 172.100.100.101/24 | 172.100.100.1 |

##### IPv6

| Management Interface | Description | Type | VRF | IPv6 Address | IPv6 Gateway |
| -------------------- | ----------- | ---- | --- | ------------ | ------------ |
| Management0 | oob_management | oob | MGMT | - | - |

#### Management Interfaces Device Configuration

```eos
!
interface Management0
   description oob_management
   no shutdown
   vrf MGMT
   ip address 172.100.100.101/24
```

### IP Name Servers

#### IP Name Servers Summary

| Name Server | VRF | Priority |
| ----------- | --- | -------- |
| 8.8.4.4 | MGMT | - |
| 8.8.8.8 | MGMT | - |

#### IP Name Servers Device Configuration

```eos
ip name-server vrf MGMT 8.8.4.4
ip name-server vrf MGMT 8.8.8.8
```

### NTP

#### NTP Summary

##### NTP Local Interface

| Interface | VRF |
| --------- | --- |
| Management0 | MGMT |

##### NTP Servers

| Server | VRF | Preferred | Burst | iBurst | Version | Min Poll | Max Poll | Local-interface | Key |
| ------ | --- | --------- | ----- | ------ | ------- | -------- | -------- | --------------- | --- |
| pool.ntp.org | MGMT | - | - | - | - | - | - | - | - |
| time.google.com | MGMT | True | - | - | - | - | - | - | - |

#### NTP Device Configuration

```eos
!
ntp local-interface vrf MGMT Management0
ntp server vrf MGMT pool.ntp.org
ntp server vrf MGMT time.google.com prefer
```

### Management API HTTP

#### Management API HTTP Summary

| HTTP | HTTPS | Default Services |
| ---- | ----- | ---------------- |
| False | True | - |

#### Management API VRF Access

| VRF Name | IPv4 ACL | IPv6 ACL |
| -------- | -------- | -------- |
| MGMT | - | - |

#### Management API HTTP Device Configuration

```eos
!
management api http-commands
   protocol https
   no shutdown
   !
   vrf MGMT
      no shutdown
```

## Authentication

### Local Users

#### Local Users Summary

| User | Privilege | Role | Disabled | Shell |
| ---- | --------- | ---- | -------- | ----- |
| admin | 15 | network-admin | False | - |

#### Local Users Device Configuration

```eos
!
username admin privilege 15 role network-admin secret sha512 <removed>
```

### AAA Authorization

#### AAA Authorization Summary

| Type | User Stores |
| ---- | ----------- |
| Exec | local |

Authorization for configuration commands is disabled.

#### AAA Authorization Device Configuration

```eos
aaa authorization exec default local
!
```

## MLAG

### MLAG Summary

| Domain-id | Local-interface | Peer-address | Peer-link |
| --------- | --------------- | ------------ | --------- |
| SPINES | Vlan4094 | 10.1.255.1 | Port-Channel491 |

Dual primary detection is disabled.

### MLAG Device Configuration

```eos
!
mlag configuration
   domain-id SPINES
   local-interface Vlan4094
   peer-address 10.1.255.1
   peer-link Port-Channel491
   reload-delay mlag 300
   reload-delay non-mlag 330
```

## Spanning Tree

### Spanning Tree Summary

STP mode: **mstp**

#### MSTP Instance and Priority

| Instance(s) | Priority |
| -------- | -------- |
| 0 | 4096 |

#### Global Spanning-Tree Settings

- Spanning Tree disabled for VLANs: **4093-4094**

### Spanning Tree Device Configuration

```eos
!
spanning-tree mode mstp
no spanning-tree vlan-id 4093-4094
spanning-tree mst 0 priority 4096
```

## Internal VLAN Allocation Policy

### Internal VLAN Allocation Policy Summary

| Policy Allocation | Range Beginning | Range Ending |
| ------------------| --------------- | ------------ |
| ascending | 1006 | 1199 |

### Internal VLAN Allocation Policy Device Configuration

```eos
!
vlan internal order ascending range 1006 1199
```

## VLANs

### VLANs Summary

| VLAN ID | Name | Trunk Groups |
| ------- | ---- | ------------ |
| 10 | INBAND_MGMT | - |
| 110 | IDF1-Data | - |
| 120 | IDF1-Voice | - |
| 130 | IDF1-Guest | - |
| 210 | IDF2-Data | - |
| 220 | IDF2-Voice | - |
| 230 | IDF2-Guest | - |
| 310 | IDF3-Data | - |
| 320 | IDF3-Voice | - |
| 330 | IDF3-Guest | - |
| 4093 | LEAF_PEER_L3 | LEAF_PEER_L3 |
| 4094 | MLAG_PEER | MLAG |

### VLANs Device Configuration

```eos
!
vlan 10
   name INBAND_MGMT
!
vlan 110
   name IDF1-Data
!
vlan 120
   name IDF1-Voice
!
vlan 130
   name IDF1-Guest
!
vlan 210
   name IDF2-Data
!
vlan 220
   name IDF2-Voice
!
vlan 230
   name IDF2-Guest
!
vlan 310
   name IDF3-Data
!
vlan 320
   name IDF3-Voice
!
vlan 330
   name IDF3-Guest
!
vlan 4093
   name LEAF_PEER_L3
   trunk group LEAF_PEER_L3
!
vlan 4094
   name MLAG_PEER
   trunk group MLAG
```

## Interfaces

### Ethernet Interfaces

#### Ethernet Interfaces Summary

##### L2

| Interface | Description | Mode | VLANs | Native VLAN | Trunk Group | Channel-Group |
| --------- | ----------- | ---- | ----- | ----------- | ----------- | ------------- |
| Ethernet3 | LEAF-1A_Ethernet49 | *trunk | *10,110,120,130 | *- | *- | 3 |
| Ethernet4 | LEAF-2A_Ethernet1/1 | *trunk | *10,210,220,230 | *- | *- | 4 |
| Ethernet5 | LEAF-3A_Ethernet49 | *trunk | *10,310,320,330 | *- | *- | 5 |
| Ethernet6 | LEAF-3B_Ethernet49 | *trunk | *10,310,320,330 | *- | *- | 5 |
| Ethernet49/1 | MLAG_PEER_spine-2_Ethernet49/1 | *trunk | *- | *- | *['LEAF_PEER_L3', 'MLAG'] | 491 |
| Ethernet50/1 | MLAG_PEER_spine-2_Ethernet50/1 | *trunk | *- | *- | *['LEAF_PEER_L3', 'MLAG'] | 491 |

*Inherited from Port-Channel Interface

##### IPv4

| Interface | Description | Type | Channel Group | IP Address | VRF |  MTU | Shutdown | ACL In | ACL Out |
| --------- | ----------- | -----| ------------- | ---------- | ----| ---- | -------- | ------ | ------- |
| Ethernet1 | P2P_LINK_TO_router-1_Ethernet1 | routed | - | 10.1.1.1/31 | default | 1500 | False | - | - |
| Ethernet2 | P2P_LINK_TO_router-2_Ethernet1 | routed | - | 10.1.1.5/31 | default | 1500 | False | - | - |

#### Ethernet Interfaces Device Configuration

```eos
!
interface Ethernet1
   description P2P_LINK_TO_router-1_Ethernet1
   no shutdown
   mtu 1500
   no switchport
   ip address 10.1.1.1/31
!
interface Ethernet2
   description P2P_LINK_TO_router-2_Ethernet1
   no shutdown
   mtu 1500
   no switchport
   ip address 10.1.1.5/31
!
interface Ethernet3
   description LEAF-1A_Ethernet49
   no shutdown
   channel-group 3 mode active
!
interface Ethernet4
   description LEAF-2A_Ethernet1/1
   no shutdown
   channel-group 4 mode active
!
interface Ethernet5
   description LEAF-3A_Ethernet49
   no shutdown
   channel-group 5 mode active
!
interface Ethernet6
   description LEAF-3B_Ethernet49
   no shutdown
   channel-group 5 mode active
!
interface Ethernet49/1
   description MLAG_PEER_spine-2_Ethernet49/1
   no shutdown
   channel-group 491 mode active
!
interface Ethernet50/1
   description MLAG_PEER_spine-2_Ethernet50/1
   no shutdown
   channel-group 491 mode active
```

### Port-Channel Interfaces

#### Port-Channel Interfaces Summary

##### L2

| Interface | Description | Type | Mode | VLANs | Native VLAN | Trunk Group | LACP Fallback Timeout | LACP Fallback Mode | MLAG ID | EVPN ESI |
| --------- | ----------- | ---- | ---- | ----- | ----------- | ------------| --------------------- | ------------------ | ------- | -------- |
| Port-Channel3 | IDF1_Po49 | switched | trunk | 10,110,120,130 | - | - | - | - | 3 | - |
| Port-Channel4 | LEAF-2A_Po11 | switched | trunk | 10,210,220,230 | - | - | - | - | 4 | - |
| Port-Channel5 | IDF3_AGG_Po49 | switched | trunk | 10,310,320,330 | - | - | - | - | 5 | - |
| Port-Channel491 | MLAG_PEER_spine-2_Po491 | switched | trunk | - | - | ['LEAF_PEER_L3', 'MLAG'] | - | - | - | - |

#### Port-Channel Interfaces Device Configuration

```eos
!
interface Port-Channel3
   description IDF1_Po49
   no shutdown
   switchport
   switchport trunk allowed vlan 10,110,120,130
   switchport mode trunk
   mlag 3
!
interface Port-Channel4
   description LEAF-2A_Po11
   no shutdown
   switchport
   switchport trunk allowed vlan 10,210,220,230
   switchport mode trunk
   mlag 4
!
interface Port-Channel5
   description IDF3_AGG_Po49
   no shutdown
   switchport
   switchport trunk allowed vlan 10,310,320,330
   switchport mode trunk
   mlag 5
!
interface Port-Channel491
   description MLAG_PEER_spine-2_Po491
   no shutdown
   switchport
   switchport mode trunk
   switchport trunk group LEAF_PEER_L3
   switchport trunk group MLAG
```

### Loopback Interfaces

#### Loopback Interfaces Summary

##### IPv4

| Interface | Description | VRF | IP Address |
| --------- | ----------- | --- | ---------- |
| Loopback0 | Router_ID | default | 10.1.253.1/32 |

##### IPv6

| Interface | Description | VRF | IPv6 Address |
| --------- | ----------- | --- | ------------ |
| Loopback0 | Router_ID | default | - |

#### Loopback Interfaces Device Configuration

```eos
!
interface Loopback0
   description Router_ID
   no shutdown
   ip address 10.1.253.1/32
```

### VLAN Interfaces

#### VLAN Interfaces Summary

| Interface | Description | VRF |  MTU | Shutdown |
| --------- | ----------- | --- | ---- | -------- |
| Vlan10 | Inband Management | default | 1500 | False |
| Vlan110 | IDF1-Data | default | - | False |
| Vlan120 | IDF1-Voice | default | - | False |
| Vlan130 | IDF1-Guest | default | - | False |
| Vlan210 | IDF2-Data | default | - | False |
| Vlan220 | IDF2-Voice | default | - | False |
| Vlan230 | IDF2-Guest | default | - | False |
| Vlan310 | IDF3-Data | default | - | False |
| Vlan320 | IDF3-Voice | default | - | False |
| Vlan330 | IDF3-Guest | default | - | False |
| Vlan4093 | MLAG_PEER_L3_PEERING | default | 1500 | False |
| Vlan4094 | MLAG_PEER | default | 1500 | False |

##### IPv4

| Interface | VRF | IP Address | IP Address Virtual | IP Router Virtual Address | VRRP | ACL In | ACL Out |
| --------- | --- | ---------- | ------------------ | ------------------------- | ---- | ------ | ------- |
| Vlan10 |  default  |  10.10.10.2/24  |  -  |  10.10.10.1  |  -  |  -  |  -  |
| Vlan110 |  default  |  10.1.10.2/23  |  -  |  10.1.10.1  |  -  |  -  |  -  |
| Vlan120 |  default  |  10.1.20.2/23  |  -  |  10.1.20.1  |  -  |  -  |  -  |
| Vlan130 |  default  |  10.1.30.2/23  |  -  |  10.1.30.1  |  -  |  -  |  -  |
| Vlan210 |  default  |  10.2.10.2/23  |  -  |  10.2.10.1  |  -  |  -  |  -  |
| Vlan220 |  default  |  10.2.20.2/23  |  -  |  10.2.20.1  |  -  |  -  |  -  |
| Vlan230 |  default  |  10.2.30.2/23  |  -  |  10.2.30.1  |  -  |  -  |  -  |
| Vlan310 |  default  |  10.3.10.2/23  |  -  |  10.3.10.1  |  -  |  -  |  -  |
| Vlan320 |  default  |  10.3.20.2/23  |  -  |  10.3.20.1  |  -  |  -  |  -  |
| Vlan330 |  default  |  10.3.30.2/23  |  -  |  10.3.30.1  |  -  |  -  |  -  |
| Vlan4093 |  default  |  10.1.254.0/31  |  -  |  -  |  -  |  -  |  -  |
| Vlan4094 |  default  |  10.1.255.0/31  |  -  |  -  |  -  |  -  |  -  |

#### VLAN Interfaces Device Configuration

```eos
!
interface Vlan10
   description Inband Management
   no shutdown
   mtu 1500
   ip address 10.10.10.2/24
   ip attached-host route export 19
   ip virtual-router address 10.10.10.1
!
interface Vlan110
   description IDF1-Data
   no shutdown
   ip address 10.1.10.2/23
   ip virtual-router address 10.1.10.1
!
interface Vlan120
   description IDF1-Voice
   no shutdown
   ip address 10.1.20.2/23
   ip virtual-router address 10.1.20.1
!
interface Vlan130
   description IDF1-Guest
   no shutdown
   ip address 10.1.30.2/23
   ip virtual-router address 10.1.30.1
!
interface Vlan210
   description IDF2-Data
   no shutdown
   ip address 10.2.10.2/23
   ip virtual-router address 10.2.10.1
!
interface Vlan220
   description IDF2-Voice
   no shutdown
   ip address 10.2.20.2/23
   ip virtual-router address 10.2.20.1
!
interface Vlan230
   description IDF2-Guest
   no shutdown
   ip address 10.2.30.2/23
   ip virtual-router address 10.2.30.1
!
interface Vlan310
   description IDF3-Data
   no shutdown
   ip address 10.3.10.2/23
   ip virtual-router address 10.3.10.1
!
interface Vlan320
   description IDF3-Voice
   no shutdown
   ip address 10.3.20.2/23
   ip virtual-router address 10.3.20.1
!
interface Vlan330
   description IDF3-Guest
   no shutdown
   ip address 10.3.30.2/23
   ip virtual-router address 10.3.30.1
!
interface Vlan4093
   description MLAG_PEER_L3_PEERING
   no shutdown
   mtu 1500
   ip address 10.1.254.0/31
!
interface Vlan4094
   description MLAG_PEER
   no shutdown
   mtu 1500
   no autostate
   ip address 10.1.255.0/31
```

## Routing

### Service Routing Protocols Model

Multi agent routing protocol model enabled

```eos
!
service routing protocols model multi-agent
```

### Virtual Router MAC Address

#### Virtual Router MAC Address Summary

Virtual Router MAC Address: 00:1c:73:00:dc:01

#### Virtual Router MAC Address Device Configuration

```eos
!
ip virtual-router mac-address 00:1c:73:00:dc:01
```

### IP Routing

#### IP Routing Summary

| VRF | Routing Enabled |
| --- | --------------- |
| default | True |
| MGMT | False |

#### IP Routing Device Configuration

```eos
!
ip routing
no ip routing vrf MGMT
```

### IPv6 Routing

#### IPv6 Routing Summary

| VRF | Routing Enabled |
| --- | --------------- |
| default | False |
| MGMT | false |

### Static Routes

#### Static Routes Summary

| VRF | Destination Prefix | Next Hop IP | Exit interface | Administrative Distance | Tag | Route Name | Metric |
| --- | ------------------ | ----------- | -------------- | ----------------------- | --- | ---------- | ------ |
| MGMT | 0.0.0.0/0 | 172.100.100.1 | - | 1 | - | - | - |

#### Static Routes Device Configuration

```eos
!
ip route vrf MGMT 0.0.0.0/0 172.100.100.1
```

### Router BGP

#### Router BGP Summary

| BGP AS | Router ID |
| ------ | --------- |
| 65001 | 10.1.253.1 |

| BGP Tuning |
| ---------- |
| no bgp default ipv4-unicast |
| maximum-paths 4 ecmp 4 |

#### Router BGP Peer Groups

##### IPv4-UNDERLAY-PEERS

| Settings | Value |
| -------- | ----- |
| Address Family | ipv4 |
| Send community | all |
| Maximum routes | 12000 |

##### MLAG-IPv4-UNDERLAY-PEER

| Settings | Value |
| -------- | ----- |
| Address Family | ipv4 |
| Remote AS | 65001 |
| Next-hop self | True |
| Send community | all |
| Maximum routes | 12000 |

#### BGP Neighbors

| Neighbor | Remote AS | VRF | Shutdown | Send-community | Maximum-routes | Allowas-in | BFD | RIB Pre-Policy Retain | Route-Reflector Client | Passive | TTL Max Hops |
| -------- | --------- | --- | -------- | -------------- | -------------- | ---------- | --- | --------------------- | ---------------------- | ------- | ------------ |
| 10.1.1.0 | None | default | - | Inherited from peer group IPv4-UNDERLAY-PEERS | Inherited from peer group IPv4-UNDERLAY-PEERS | - | - | - | - | - | - |
| 10.1.1.4 | None | default | - | Inherited from peer group IPv4-UNDERLAY-PEERS | Inherited from peer group IPv4-UNDERLAY-PEERS | - | - | - | - | - | - |
| 10.1.254.1 | Inherited from peer group MLAG-IPv4-UNDERLAY-PEER | default | - | Inherited from peer group MLAG-IPv4-UNDERLAY-PEER | Inherited from peer group MLAG-IPv4-UNDERLAY-PEER | - | - | - | - | - | - |

#### Router BGP Device Configuration

```eos
!
router bgp 65001
   router-id 10.1.253.1
   maximum-paths 4 ecmp 4
   no bgp default ipv4-unicast
   neighbor IPv4-UNDERLAY-PEERS peer group
   neighbor IPv4-UNDERLAY-PEERS send-community
   neighbor IPv4-UNDERLAY-PEERS maximum-routes 12000
   neighbor MLAG-IPv4-UNDERLAY-PEER peer group
   neighbor MLAG-IPv4-UNDERLAY-PEER remote-as 65001
   neighbor MLAG-IPv4-UNDERLAY-PEER next-hop-self
   neighbor MLAG-IPv4-UNDERLAY-PEER description spine-2
   neighbor MLAG-IPv4-UNDERLAY-PEER send-community
   neighbor MLAG-IPv4-UNDERLAY-PEER maximum-routes 12000
   neighbor MLAG-IPv4-UNDERLAY-PEER route-map RM-MLAG-PEER-IN in
   neighbor 10.1.1.0 peer group IPv4-UNDERLAY-PEERS
   neighbor 10.1.1.0 remote-as None
   neighbor 10.1.1.0 local-as None no-prepend replace-as
   neighbor 10.1.1.0 description router-1
   neighbor 10.1.1.4 peer group IPv4-UNDERLAY-PEERS
   neighbor 10.1.1.4 remote-as None
   neighbor 10.1.1.4 local-as None no-prepend replace-as
   neighbor 10.1.1.4 description router-2
   neighbor 10.1.254.1 peer group MLAG-IPv4-UNDERLAY-PEER
   neighbor 10.1.254.1 description spine-2
   redistribute attached-host
   redistribute connected
   !
   address-family ipv4
      neighbor IPv4-UNDERLAY-PEERS activate
      neighbor MLAG-IPv4-UNDERLAY-PEER activate
```

## Multicast

### IP IGMP Snooping

#### IP IGMP Snooping Summary

| IGMP Snooping | Fast Leave | Interface Restart Query | Proxy | Restart Query Interval | Robustness Variable |
| ------------- | ---------- | ----------------------- | ----- | ---------------------- | ------------------- |
| Enabled | - | - | - | - | - |

#### IP IGMP Snooping Device Configuration

```eos
```

## Filters

### Prefix-lists

#### Prefix-lists Summary

##### PL-L2LEAF-INBAND-MGMT

| Sequence | Action |
| -------- | ------ |
| 10 | permit 10.10.10.0/24 |

#### Prefix-lists Device Configuration

```eos
!
ip prefix-list PL-L2LEAF-INBAND-MGMT
   seq 10 permit 10.10.10.0/24
```

### Route-maps

#### Route-maps Summary

##### RM-CONN-2-BGP

| Sequence | Type | Match | Set | Sub-Route-Map | Continue |
| -------- | ---- | ----- | --- | ------------- | -------- |
| 20 | permit | ip address prefix-list PL-L2LEAF-INBAND-MGMT | - | - | - |

##### RM-MLAG-PEER-IN

| Sequence | Type | Match | Set | Sub-Route-Map | Continue |
| -------- | ---- | ----- | --- | ------------- | -------- |
| 10 | permit | - | origin incomplete | - | - |

#### Route-maps Device Configuration

```eos
!
route-map RM-CONN-2-BGP permit 20
   match ip address prefix-list PL-L2LEAF-INBAND-MGMT
!
route-map RM-MLAG-PEER-IN permit 10
   description Make routes learned over MLAG Peer-link less preferred on spines to ensure optimal routing
   set origin incomplete
```

## VRF Instances

### VRF Instances Summary

| VRF Name | IP Routing |
| -------- | ---------- |
| MGMT | disabled |

### VRF Instances Device Configuration

```eos
!
vrf instance MGMT
```
