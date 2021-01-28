# Networking

## IPIP 

- no encryption involved
- ip package inside another ip package (ipv4)
- no for multicast
- one IPIP linke between two hosts



```
#--- server
# modprobe ipip
# ip tunnel  add tunnellab mode ipip remote 10.30.30.27 local 10.30.30.245 ttl 255
# ip a s 
# tunnellab@NONE: <POINTOPOINT,NOARP> mtu 1480 qdisc noop state DOWN group defa│
ult qlen 1000   
# ip a a 10.40.37.10/24 dev tunnellab     
# ip link set dev tunnellab up

#-- client
ip tunnel  add tunnellab mode ipip remote 10.30.30.245 local 10.30.30.27 ttl 255
```

## GRE

- `modprobe ip_gre` m
- supports unicast to multicast traffic.
- multiples tunnels


```
# -side 1
ip tunnel add gre_tunnel mode gre remote 10.30.30.245 local 10.30.30.27 
ip link
ip link set dev grep_tunnel up
ip link set dev gre_tunnel up
ip a a 11.0.0.1/25 dev gre_tunnel

# -side 2 (repeate)

```
## NAT (Network Address Translation)

TODO: add something


- iptables masquerade
- forward and accept related,establish traffic
- `net.sys.ipv4.ip_forward=1`

## Dynamic routing traffic

- Quagga/Zebra (OSPF/RIP/BGP)


