Hostname
```
hostname ASA
```

Interfaces
```
interface GigabitEthernet1/1
no shut
bridge-group 1
nameif outside1
security-level 0

interface GigabitEthernet1/2
no shut
bridge-group 2
nameif inside1
security-level 100

interface GigabitEthernet1/3
no shut
bridge-group 2
nameif inside2
security-level 100

interface GigabitEthernet1/4
no shut
nameif dmz
security-level 50
ip address 172.17.19.1 255.255.255.0 

interface GigabitEthernet1/5
no shut
bridge-group 1
nameif outside2
security-level 0

interface BVI1
nameif inside
security-level 100
ip address 172.18.19.1 255.255.255.0 

interface BVI2
nameif outside
security-level 0
ip address 172.16.19.2 255.255.255.0 
```

Object Networks
```
object network outside1
subnet 172.16.19.0 255.255.255.0
nat (inside1,outside1) dynamic interface

object network outside2
subnet 172.16.19.0 255.255.255.0
nat (inside1,outside2) dynamic interface

object network inside1
subnet 172.18.19.0 255.255.255.0
nat (inside1,outside1) dynamic interface

object network inside2
subnet 172.18.19.0 255.255.255.0
nat (inside2,outside2) dynamic interface

object network dmz
subnet 172.17.19.0 255.255.255.0
nat (dmz,outside1) dynamic interface
```

DHCP
```
dhcpd address 172.18.19.2-172.18.19.253 inside
dhcpd dns 8.8.8.8 interface inside
dhcpd option 150 ip 172.16.19.1 interface inside
dhcpd enable inside
```
Default Outside Route
```
route outside 0.0.0.0 0.0.0.0 172.16.19.1
```
Access-Lists
```
access-list OUT extended permit ip any any 
access-list OUT extended permit tcp any any
access-list OUT extended permit udp any any
access-list OUT extended permit icmp any any echo
access-list OUT extended permit icmp any any echo-reply
access-list IN extended permit ip any any 
access-list IN extended permit tcp any any
access-list IN extended permit udp any any
access-list IN extended permit icmp any any echo
access-list IN extended permit icmp any any echo-reply
access-list DMZ extended permit ip any any 
access-list DMZ extended permit tcp any any
access-list DMZ extended permit udp any any
access-list DMZ extended permit icmp any any echo
access-list DMZ extended permit icmp any any echo-reply

access-group OUT in interface outside
access-group OUT out interface outside
access-group OUT in interface outside1
access-group OUT out interface outside1
access-group OUT in interface outside2
access-group OUT out interface outside2
access-group IN in interface inside
access-group IN out interface inside
access-group IN in interface inside1
access-group IN out interface inside1
access-group IN in interface inside2
access-group IN out interface inside2
access-group DMZ in interface dmz
access-group DMZ out interface dmz
```
