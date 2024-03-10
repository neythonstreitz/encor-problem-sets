# Multi-area OSPF Lab


## Preliminary Questions
Before beginning the lab, take some time to think about these questions:
- What purpose do areas serve in OSPF design?
    - Areas allow for hierarchical/segmented OSPF design. 
    By grouping devices into areas, we can minimize LSAs flooding throughout the network, reduce the size of the LSDB on each device, and enact greater control on the network.
- How does OSPF calculate the cost of a link?
    - Cost = link speed / reference bandwidth (default 100 Mbps, rounded to 1 if <1)
- What are the OSPFv2 states?
    - DOWN, INIT, 2-WAY, EXSTART, EXCHANGE, LOADING, FULL (Make sure you know what happens in each state!)
- What is the purpose of route summarization for OSPF?
    - Helps reduce OSPF traffic throughout the network and reduce router computation.

## Lab Introduction
In this lab, you'll practice configuring and examining multi-area OSPFv2.

## Lab Instructions
Before starting, ensure lab environment has succesfully been imported and all devices are on.

### Step 1: Configure OSPF for SW1, R1, and R2
In this step, configure OSPFv2 on the listed devices, ensuring all local networks are being shared.

1.1 Configure OSPFv2 on SW1 (Internal Router)
    - Enable IP routing
    - Manually set the router ID to 1.1.1.2
    - Set the OSPF cost such that we cam distinguish between Gigabit Ethernet and Fast Ethernet interfaces.
    - Advertise all four of SW1's connected interfaces into the appropriate area.

```
router ospf 100
    router-id 1.1.1.2
    auto-cost reference-bandwidth 1000
    network 10.10.0.0 0.15.255.255 area 1
exit

```

1.2 Configure OSPFv2 on R1 (Area Border Router)
    - Enable IP routing
    - Manually set the router ID to 1.1.1.1
    - Set the OSPF cost such that we cam distinguish between Gigabit Ethernet and Fast Ethernet interfaces.
    - Advertise both connected interfaces to the appropriate areas.

```
router ospf 100
    router-id 1.1.1.1
    auto-cost reference-bandwidth 1000
    network 10.10.0.0 0.0.0.3 area 1
    network 172.16.0.0 0.0.0.3 area 0
exit


```

1.3 Configure OSPFv2 on R2 (Autonomous System Boundary Router)
    - Enable IP routing
    - Manually set the router ID to 2.2.2.1
    - Set the OSPF cost such that we cam distinguish between Gigabit Ethernet and Fast Ethernet interfaces.
    - Advertise both connected interfaces to the appropriate area.
    - Configure R2 to propogate a default route to the Internet.
    - Ensure a default route to the Internet exists.

```
router ospf 100
    router-id 2.2.2.1
    auto-cost reference-bandwidth 1000
    network 172.16.0.0 0.0.0.3 area 0
    network 172.16.1.0 0.0.0.3 area 0
    default-information originate
exit

ip route 0.0.0.0 0.0.0.0 Loopback0
```

### Step 2: Verify the OSPFv2 network has been established.
In this step, we will verify that the configured OSPF network was correctly implemented.

2.1 Verify OSPFv2 for SW1
    - Examine the active OSPF interfaces and their assigned areas.
        - ```show ip ospf interface brief```
    - What is the IP address of the designated router?
        - 10.10.1.1
    - Examine the neighbor adjacency established by SW1.
        - ```show ip ospf neighbor```
    - What state is the neighbor adjacency in?
        - FULL

2.2 Verify OSPFv2 for R1 (FOLLOW THE SAME STEPS AS ABOVE!)
    - Examine the active OSPF interfaces and their assigned areas.
    - What is the IP address of the designated router?
    - Examine the neighbor adjacency established by R1.
    - What state is the neighbor adjacency in?

2.3 Verify OSPFv2 for R2 (FOLLOW THE SAME STEPS AS ABOVE)
    - Examine the active OSPF interfaces and their assigned areas.
    - What is the IP address of the designated router?
    - Examine the neighbor adjacency established by R1.
    - What state is the neighbor adjacency in?
    - Verify the gateway of last resort is succesfully being advertised.   
  
### Step 3: Route Summarization
In this step, we will use route summarization on R3 to minimize the routes being shared.

3.1 Calculate the OSPFv2 summary route
    - Identify all Loopback addresses (representing networks) currently being shared into OSPF.
        - 10.10.16.0/24 -> 10.10.23.0/24 (8 networks total)
    - Starting from the left, how many binary bits match across all networks?
        - 21 bits match. 
    - Use the above number to calculate the new subnet mask, along with the network address.
        - Since 21 bits match, we'll want a /21 network meaning the subnet mask is 255.255.248.0 and the network address is 10.0.16.0.

3.2 Advertise the summarized routes.
    - Configure the new summary route for the Loopback interfaces.

```
router ospf 100
    area 2 range 10.10.16.0 255.255.248.0 cost 65
```

3.3 Verify route summarization
    - Examine the routing table of R3.
        - ```show ip route ospf```
    - Why was a Null0 destination created?
        - To prevent loops when traffic is sent within the summarized range but not actually a connected network. Instead, we just drop it.
    - Examine the routing table of R2.
        - ```show ip route ospf```
    - Are we only recieving the summarized route or all individual Loopbacks?
        - Only the summary route!

