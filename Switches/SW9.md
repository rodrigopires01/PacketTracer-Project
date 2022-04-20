Hostname
```
hostname SW9
```
VLANS
```
vlan 10
name sales
exit

vlan 20
name marketing
exit

vlan 30
name prod
exit
```
STP(Spanning Tree Protocol)
```
spanning-tree vlan 20 priority 28672
spanning-tree vlan 30 priority 24576
```
Interfaces
```
interface Port-channel1
switchport mode trunk

interface FastEthernet0/11
switchport mode trunk
channel-protocol lacp
channel-group 1 mode active

interface FastEthernet0/12
switchport mode trunk
channel-protocol lacp
channel-group 1 mode active
         
interface FastEthernet0/13
switchport mode trunk
```
