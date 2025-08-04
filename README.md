# proxmox-vlan-homelab
Documenting the transition from a flat network to VLan aware network.


Switch: Netgear GS108Ev4, VLAN-capable.
VLANs configured: Yes (e.g., Default, Management, Cluster, Monitoring, Security, etc.).
Current mode: Basic 802.1Q VLAN with ports mostly in Access mode for VLAN 1.
# Transition plan:
- Move 4-node Proxmox cluster to a dedicated VLAN (e.g., VLAN 20 - "Cluster").
- Add bare-metal Ubuntu server to VLAN.
- Use OPNsense HA (CARP) setup with:
- One Node using traditional dual-NIC routing.
- One Node using Router on a Stick (single NIC with VLAN tags).
Eventually, most ports will be Trunk, except port 8 (Access to VLAN 1, connected to Wi-Fi extender).

# Phase 1:  Enabling VLan on the First Node:
Trunk Port with Native (Untagged) VLAN Allowed
Leave VLAN 1 untagged on the trunk port (on the switch).

vmbr0 receives untagged traffic → handled by Proxmox's main interface (e.g., eno1 with IP 192.168.0.X).
vmbr0.20 receives VLAN 20 tagged traffic → handled by Proxmox’s virtual VLAN interface with IP 10.0.20.X.
Result: Dual stack — same NIC handles both networks.

Proposed VLAN/Subnet Map:
```
VLAN ID    Purpose        Subnet
1          Default/Mgmt   10.0.0.0/24
10         Management     10.0.10.0/24
20         Cluster        10.0.20.0/24
30         Monitoring     10.0.30.0/24
40         Storage        10.0.40.0/24
50         General        10.0.50.0/24
60         ---            10.0.60.0/24
70         ---            10.0.70.0/24
80         Security       10.0.80.0/24
99         WAN / CARP     Public/RFC1918
