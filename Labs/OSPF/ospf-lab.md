# Multi-area OSPF Lab


## Preliminary Questions
Before beginning the lab, take some time to think about these questions:
- What purpose do areas serve in OSPF design?
- How does OSPF calculate the cost of a link?
- What are the OSPFv2 states?
- What is the purpose of route summarization for OSPF?

## Lab Introduction
In this lab, you'll practice configuring and examining multi-area OSPFv2.

## Lab Instructions
Before starting, ensure lab environment has succesfully been imported and all devices are on.

### Step 1: Configure OSPF for D1, R1, and R2
In this step, configure OSPFv2 on the listed devices, ensuring all local networks are being shared.

1.1 Configure OSPFv2 on D1 (Internal Router)
    - Enable IP routing
    - Manually set the router ID to 1.1.1.2
    - Set the OSPF cost such that we cam distinguish between Gigabit Ethernet and Fast Ethernet interfaces.
    - Advertise all four of D1's connected interfaces into the appropriate area.

1.2 Configure OSPFv2 on R1 (Area Border Router)
    - Enable IP routing
    - Manually set the router ID to 1.1.1.1
    - Set the OSPF cost such that we cam distinguish between Gigabit Ethernet and Fast Ethernet interfaces.
    - Advertise both connected interfaces to the appropriate areas.

1.3 Configure OSPFv2 on R2 (Autonomous System Boundary Router)
    - Enable IP routing
    - Manually set the router ID to 2.2.2.1
    - Set the OSPF cost such that we cam distinguish between Gigabit Ethernet and Fast Ethernet interfaces.
    - Advertise both connected interfaces to the appropriate area.
    - Configure R2 to propogate a default route to the Internet.
    - Ensure a default route to the Internet exists.

### Step 2: Verify the OSPFv2 network has been established.
In this step, we will verify that the configured OSPF network was correctly implemented.

2.1 Verify OSPFv2 for D1
    - Examine the active OSPF interfaces and their assigned areas.
    - What is the IP address of the designated router?
    - Examine the neighbor adjacency established by D1.
    - What state is the neighbor adjacency in?

2.2 Verify OSPFv2 for R1
    - Examine the active OSPF interfaces and their assigned areas.
    - What is the IP address of the designated router?
    - Examine the neighbor adjacency established by R1.
    - What state is the neighbor adjacency in?

2.3 Verify OSPFv2 for R2
    - Examine the active OSPF interfaces and their assigned areas.
    - What is the IP address of the designated router?
    - Examine the neighbor adjacency established by R1.
    - What state is the neighbor adjacency in?
    - Verify the gateway of last resort is succesfully being advertised.   
  
### Step 3: Route Summarization
In this step, we will use route summarization on R3 to minimize the routes being shared.

3.1 Calculate the OSPFv2 summary route
    - Identify all Loopback addresses (representing networks) currently being shared into OSPF.
    - Starting from the left, how many binary bits match across all networks?
    - Use the above number to calculate the new subnet mask, along with the network address.
    - Repeat this process with the connected interfaces (10.0.4.0/30 and 10.0.5.0/30).

3.2 Advertise the summarized routes.
    - Configure the new summary route for the Loopback interfaces.
    - Configure the new summary route for the connected interfaces.

3.3 Verify route summarization
    - Examine the routing table of 
