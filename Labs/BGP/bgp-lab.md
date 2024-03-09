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
  - Advertise the IPv4 prefixes local to ASN 100.

1.2 Implement BGP and neighbor relationships on R2.
  - Configure all appropriate designated neighbors on R2.
  - Advertise the IPv4 prefixes local to ASN 500.

### Step 2: Troubleshoot eBGP relationships
At this point, the BGP process should be running across all routers in the environment. Is the configuration succesful?

2.1 Examine the routing tables for R1, R2, and R3.
  - Are all routers recieving the appropriate routes from each other?
  - What routes, if any, are missing? Why?

2.2 Examine the BGP neighbor relationships between R2 <--> R1 and R2 <--> R3.
 - What is the BGP state of each BGP neighbor?

2.3 Complete the BGP configuration on R3 to fix the neighbor relationships.
 - HINT: By default, **bgp default ipv4-unicast** is enabled... How do we manually initialize an address family and activate a neighbor for a specific shared route?
2.4 Examine the routing tables once more for R1, R2, and R3.
 - Have we succesfully troubleshooted BGP? How do you know?

### Step 3: Verify eBGP 
In this step, use show commands to examine BGP operation.

3.1 Examine the contents of the BGP routing table for R1.
  - What does the * at the beginning of an entry indicate?
  - What deos the angle bracket (>) in an entry indicate?
  - What is the address of the preferred next hop router to reach the 192.168.3.0/27? Why?

### Step 4: Configure route summarization 
In this step, use summarization to conserve router resources and improve stability.

4.1 Configure R1 to advertise a summary route.
  - The customer has been allocated the entire 192.168.1.0/24 prefix.
  - Ensure only the summarized route is advertised.

4.2 Configure R3 to advertise a summary route.
  - The customer has been allocated the entire 192.168.3.0/24 prefix.
  - Ensure only the summarized route is advertised.
  - Hint: Rembember that R3 must be configured in IPv4 address family configuration mode.

4.3 Examine the routing table on R2.
  - Verify that R2 is only recieving aggregate routes from both R1 and R3.

4.4 Examine the routing table on R1 and R3.
  - Why do R1 and R3 contain an entry with a next hop address of Null0?
  - Why do R1 and R3 contain entires with a status code of "s"?
