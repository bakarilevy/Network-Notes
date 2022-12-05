Configurations can be set in any valid text file, using ios extension for integration with VSCode cisco ios syntax extension

In configuration terminal mode (conf t) you can exit by typing ctrl-z

To save a running-config to startup run the command: copy running-config startup-config / write memory

To transfer files over ftp use copy ftp command

Wide area networks are used for connections to the interenet via ISP or connecting two office LANs over a private network

For an edge router T1 connection to an ISP, configurations should be provided by them, if not we require:
- Serial Interface IP Address: Most likely CIDR 30
- Circuit ID or Customer Number: In the event of T1 failure contact ISP and provide Circuit ID or Customer Number
- T1 Line Encapsulation: All T1s have encapsulation setting, most commonly HDLC or PPP
- Ethernet IP Addresses: Should receive a block of IP Addresses for machines on the network, assignable to firewalls, desktops, etc.

WAN ISP Link Example:
```
conf t
int s1/0
description ISP Uplink, Circuit ID #000000, Customer Number #1111111
encapsulation HDLC
ip address 11.10.9.8 255.255.255.252
shut
no shut

! Setting default route where next-hop is 11.10.9.7 ISP Side T1 Circuit
ip route 0.0.0.0 0.0.0.0 11.10.9.7

write memory
```

WAN Private Link Example:

Main Office Router
```
conf t
int s1/0
ip address 10.0.3.5 255.255.255.252
shut
no shut

! Send all traffic for 10.0.2.0 network to Branch Office Router
ip route 10.0.2.0 255.255.255.0 10.0.3.6

! Send any other traffic to ISP Edge Router
ip route 0.0.0.0 10.0.1.1
```

Branch Office Router
```
conf t
int s1/0
ip address 10.0.3.6 255.255.255.252
shut
no shut

! Send all traffic to the Headquarters router:
ip route 0.0.0.0 0.0.0.0 10.0.3.5
```

Cisco devices should "just work" but in the event of obscure errors the SmartNet Contract is preferred.

Copying files over ftp:
copy flash:c3640-is56i-mz-120-7-XK1.bin ftp://example:password@ftpserver/c3640-is56i-mz-120-7-XK1.bin

## Border Gateway Protocol

Border Gateway Protocol (BGP) announces to the world what IP Addresses your router is responsible for.
Each Autonomous System (AS) represents a single network of any size that makes its own routing decisions.
If you have two ISPs the network has to make a decision about where to send a stream of traffic, allowing you to adjust how traffic enters and leaves the net. Any two Autonomous Systems that are directly connected and exchange routing information over BGP are considered peers. Each AS tells its peer which IP Addresses can be reached in that AS via route announcements. A peer will tell your router which ip addresses each of its respective peers has and how many other AS a packet will need to travel through. An AS path of 6 through your primary internet service provider would not be preferred by your router compared to an AS path of 3 offered by your secondary ISP. BGP does not load balance across two ISP links, it will always take the path with the least number of AS hops, this could mean always favoring one ISP link over another.

The American Registry for Internet Numbers assigns ASNs, you must have at least a CIDR 24 network to register.
Email the request to: hostmaster@arin.net with subject ASN REQUEST

In order to advertise routes over BGP, the routes must already be in the routing table.
If you have an address block of: 192.168.0.0/23, you must have a single static route for the whole /23 block in the routing table:

```
ip route 192.168.0.0 255.255.254.0 null0
access-list 130 permit ip host 192.168.0.0 host 255.255.254.0

router bgp 100
bgp dampening
network 192.168.0.0 mask 255.255.254.0
redistribute static

neighbor 10.1.8.5 remote-as 200
neighbor 10.1.8.5 route-map isp-verizon-out out

neighbor 172.16.0.2 remote-as 300
neighbor 172.16.0.2 route-map isp-comcast-out out

route-map isp-verizon-out
match ip address 130

route-map isp-comcast-out
match ip address 130
```

Above we start by adding a null summary route to cover our entire 192.168.0.0/23 network (in our routing table we already have more exact matches for different subnets). We create an access-list to match against for our null summary route. We create the basic BGP configurations with dampening to avoid constantly readvertising routes if changes are being made in the routing table in quick successsion. We add a network statement for the null route summarizing our entire network. We add the static route to the BGP table. We attach the route maps we advertise out to each BGP peer and we complete the configuration for each route map.

Run the commmand "show ip bgp summary" to see a brief breakdown of BGP routes
