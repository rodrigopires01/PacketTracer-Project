Hostname
```
hostname R1-Alpha
```

DHCP Server for ASA
```
ip dhcp excluded-address 172.16.19.1
ip dhcp excluded-address 172.16.19.253
ip dhcp excluded-address 172.18.19.253
ip dhcp excluded-address 172.18.19.1

ip dhcp pool inside
network 172.18.19.0 255.255.255.0
default-router 172.18.19.253
option 150 ip 172.18.19.1
dns-server 8.8.8.8
exit
```

Access-Lists to do NAT, VPN and Route HTTP/HTTPS packets to the server.
```
access-list 1 permit 10.0.0.0 0.255.255.255
access-list 1 permit 172.16.0.0 0.15.255.255
access-list 1 permit 192.168.0.0 0.0.255.255

access-list 100 permit tcp any any eq www
access-list 100 permit tcp any any eq 443
access-list 100 permit ip any 1.19.0.0 0.0.0.3

access-list 101 permit gre host 1.19.0.2 host 1.19.0.1
```

NAT & PAT
```
ip nat inside source list 1 interface Serial0/1/1 overload
ip nat inside source static tcp 172.17.19.2 80 1.19.0.2 80 
ip nat inside source static tcp 172.17.19.2 443 1.19.0.2 443
```

Static Routes to the DMZ and INSIDE
```
ip route 172.17.19.0 255.255.255.0 172.16.19.2 
ip route 172.18.19.0 255.255.255.0 172.16.19.2
```

IPsec Configuration
```
crypto map OMAPA 10 ipsec-isakmp 
set peer 1.19.0.1
match address 101

crypto isakmp key Passw0rd address 1.19.0.1
crypto map OMAPA 10 ipsec-isakmp 
crypto ipsec transform-set TRANS ah-sha-hmac esp-aes 256 esp-sha-hmac

crypto map OMAPA 10 ipsec-isakmp 
set transform-set TRANS 

crypto isakmp policy 10
authentication pre-share
encr aes 256
lifetime 10000
group 5
```

Interfaces
```
interface FastEthernet0/0
ip address 172.16.19.1 255.255.255.0
ip nat inside
no shutdown

interface Serial0/1/1
ip address 1.19.0.2 255.255.255.252
ip nat outside
crypto map OMAPA
no shutdown

interface Tunnel100
no shutdown
ip address 192.168.19.2 255.255.255.252
tunnel source Serial0/1/1
tunnel destination 1.19.0.1
```

VOIP
```
telephony-service
max-ephones 10
max-dn 10
ip source-address 172.18.19.1 port 2000
auto assign 1 to 10

ephone-dn 1
number 200
ephone-dn 2
number 201
```

EIGRP
```
router eigrp 100
redistribute static 
network 192.168.19.0 0.0.0.3
network 172.16.19.0 0.0.0.255
network 172.18.19.0 0.0.0.255
network 172.17.19.0 0.0.0.255
passive-interface FastEthernet0/0
```
