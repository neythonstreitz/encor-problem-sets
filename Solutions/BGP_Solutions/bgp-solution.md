# eBGP Lab
3.2.c Configure and verify eBGP between directly connected neighbors (best path selection algorithm and neighbor relationships)

## Preliminary Questions
Before beginning the lab, take some time to think about these questions:
- What is an autonomous system? What role do ASN's play in BGP operation?
- What is the BGP Best Path Selection algorithm?
- What is route summarization? How does route summarization make networks more efficient?


## Lab Introduction
In this lab, you'll practice configuring and verifying eBGP relationships between neighbor routers and advertising summarized routes.


## Lab Instructions
Before starting, ensure lab environment has succesfully been implemented. 
Interfaces should be up and configured with the correct IP addressing schemes. 

### Step 1: Configure eBGP.
In this step, configure eBGP for both R1 and R2. Note that R3 has already been configured and will need to be troubleshooted in a following step.

1.1 Implement BGP and neighbor relationships on R1.
  - Configure all appropriate designated neighbors on R1.
  - Set the router-id to 1.1.1.1
  - Advertise the IPv4 prefixes local to ASN 100.
```
en
conf t

router bgp 100
bgp router-id 1.1.1.1
neighbor 10.1.2.2 remote-as 500
neighbor 10.1.3.2 remote-as 300
neighbor 10.1.3.130 remote-as 300

network 192.168.1.0 mask 255.255.255.224
network 192.168.1.64 mask 255.255.255.192

exit
```

1.2 Implement BGP and neighbor relationships on R2.
  - Configure all appropriate designated neighbors on R2.
  - Set the router-id to 2.2.2.2
  - Advertise the IPv4 prefixes local to ASN 500.

```
en
conf t

router bgp 500
bgp router-id 2.2.2.2
neighbor 10.1.2.1 remote-as 100
neighbor 10.2.3.2 remote-as 300

network 192.168.2.0 mask 255.255.255.224
network 192.168.2.64 mask 255.255.255.192

exit
```

### Step 2: Troubleshoot eBGP relationships
At this point, the BGP process should be running across all routers in the environment. Is the configuration succesful?

2.1 Examine the routing tables for R1, R2, and R3.
  - Are all routers recieving the appropriate routes from each other?
    - ```show ip bgp``` -> Nope! R1 and R2 are sharing but R3 isn't.
  - What routes, if any, are missing? Why?
    - ```show ip route bgp``` -> Routes from R3 (the loopbacks) are missing from R1 and R2's tables.

2.2 Examine the BGP neighbor relationships between R2 <--> R1 and R2 <--> R3.
 - What is the BGP state of each BGP neighbor?
   - ```show ip bgp neighbors``` -> R2 and R1 are in an ESTABLISHED state, but R2 and R3 are in an IDLE state.

2.3 Complete the BGP configuration on R3 to fix the neighbor relationships.
 - HINT: By default, **bgp default ipv4-unicast** is enabled... How do we manually initialize an address family and activate a neighbor for a specific shared route?

```
en
conf t

router bgp 300
address-family ipv4
neighbor 10.1.2.2 activate
neighbor 10.1.3.2 activate
neighbor 10.1.3.129 activate
network 192.168.1.0 mask 255.255.255.224
network 192.168.1.64 mask 255.255.255.192

exit
```

2.4 Examine the routing tables once more for R1, R2, and R3.
 - Have we succesfully troubleshooted BGP? How do you know?
   - If we run any of our previous show commands, you can see that routes are being shared across all routers and the BGP state between R3 and its neighbors is ESTABLISHED. 

### Step 3: Verify eBGP 
In this step, use show commands to examine BGP operation.

3.1 Examine the contents of the BGP routing table for R1. ```show ip bgp```
  - What does the * at the beginning of an entry indicate?
    - A VALID route for BGP to use!
  - What deos the angle bracket (>) in an entry indicate?
    - The BEST route that BGP had identified using the Best Path algorithm.
  - What is the address of the preferred next hop router to reach the 192.168.3.0/27? Why?
    - 10.1.3.1 since it is has the > for best route.

### Step 4: Configure route summarization 
In this step, use summarization to conserve router resources and improve stability.

4.1 Configure R1 to advertise a summary route.
  - The customer has been allocated the entire 192.168.1.0/24 prefix.
  - Ensure only the summarized route is advertised.

```
en
conf t

router bgp 100
aggregate-address 192.168.1.0 255.255.255.0 summary-only
```
4.2 Configure R3 to advertise a summary route.
  - The customer has been allocated the entire 192.168.3.0/24 prefix.
  - Ensure only the summarized route is advertised.
  - Hint: Rembember that R3 must be configured in IPv4 address family configuration mode.

```
en
conf t

router bgp 300
address-family ipv4
aggregate-address 192.168.3.0 255.255.255.0 summary-only
```

4.3 Examine the routing table on R2.
  - Verify that R2 is only recieving aggregate routes from both R1 and R3.
    - ```show ip route bgp``` -> We can see that R2 can only see the 192.168.1.0/24 and 192.168.3.0/24 networks and not the two individual networks.

4.4 Examine the routing table on R1 and R3. ```show ip bgp```
  - Why do R1 and R3 contain an entry with a next hop address of Null0?
    - This is to prevent loops! If a R2 sends R1 a packet destined for 192.168.1.254, R1 won't have that network and probably send it back to its default route! With the null route, we can 'black hole'/drop it right there!
  - Why do R1 and R3 contain entires with a status code of "s"?
    - This means that BGP is aware of those networks locally, but will not advertise them. This is because we used the summary-only command to only advertise our aggregate address!
