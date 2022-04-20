Hostname
```
hostname R4-Beta
```
Access-Lists to do NAT, VPN and Route HTTP/HTTPS packets to the server.
```
access-list 1 permit 172.16.0.0 0.15.255.255
access-list 1 permit 192.168.0.0 0.0.255.255
access-list 1 permit 10.0.0.0 0.255.255.255
access-list 100 permit tcp any any eq www
access-list 100 permit tcp any any eq 443
access-list 100 permit ip any 2.19.0.0 0.0.0.3
access-list 101 permit gre host 2.19.0.2 host 2.19.0.1
```
NAT & PAT
```
ip nat inside source list 1 interface Serial0/1/1 overload
ip nat inside source static tcp 172.17.19.2 80 2.19.0.2 80 
ip nat inside source static tcp 172.17.19.2 443 2.19.0.2 443
```
IPsec Configuration with Encryption
```
crypto isakmp key Passw0rd address 2.19.0.1
crypto map UMAPA 10 ipsec-isakmp 
crypto ipsec transform-set TRANS ah-sha-hmac esp-aes 256 esp-sha-hmac

crypto map UMAPA 10 ipsec-isakmp 
set transform-set TRANS

crypto isakmp policy 10
authentication pre-share
encr aes 256
lifetime 10000
group 5

crypto map UMAPA 10 ipsec-isakmp
set peer 2.19.0.1
match address 101
```
Interfaces
```
interface FastEthernet0/0
no shutdown
ip address 172.16.19.3 255.255.255.0
ip nat inside

interface Serial0/1/1
no shutdown
ip address 2.19.0.2 255.255.255.252
ip nat outside
crypto map UMAPA

interface Tunnel200
no shutdown
ip address 192.168.19.6 255.255.255.252
tunnel source Serial0/1/1
tunnel destination 2.19.0.1
```
EIGRP
```
router eigrp 100
network 192.168.19.4 0.0.0.3
network 172.16.19.0 0.0.0.255
network 172.17.19.0 0.0.0.255
network 172.18.19.0 0.0.0.255
network 172.19.19.0 0.0.0.255
redistribute static
passive-interface FastEthernet0/0
```
