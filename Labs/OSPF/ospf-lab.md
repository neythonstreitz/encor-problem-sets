# Multi-area OSPF Lab

## Lab Introduction
In this lab, you'll practice configuring, examining, and troubleshooting multi-area OSPFv2.
One side of the network requires implementing OSPFv2 from scratch.
The other side of the network requires identifying and fixing broken OSPFv2 connections.
Additionally, this lab includes route summarization.

#### Exam Topics
> 3.2 Layer 3
>
> - 3.2.b Configure simple OSPFv2/v3 environments, including multiple normal areas, summarization, and filtering (neighbor adjacency, point-to-point, and broadcast network types, and passive-interface)

#### Lab Topology
![OSPF Lab Topology](./Images/ospf-lab-topology.png)

#### Lab Devices
| Device | Image | Connectivity | Notes |
| ----------- | ----------- | ----------- | ----------- | 
| D1 | IOSv | **G0/0:** 10.10.0.2 <br> **G0/3:** 10.10.1.1 | **Router ID:** 1.1.1.2 |
| R1 | IOSv | **G0/0:** 10.10.0.1 <br> **G0/1:** 172.16.0.2 | **Router ID:** 1.1.1.1 | 
| R2 | IOSv | **G0/1:** 172.16.0.1 <br> **G0/2:** 172.16.1.1 <br> **L0:** 192.168.200.255 | **Router ID:** 2.2.2.1 |
| R3 | IOSv | **G0/2:** 172.16.1.2 <br> **G0/0:** 10.10.4.1 | **Router ID:** 3.3.3.1 |
| D2 | IOSv | **G0/0:** 10.10.4.2 <br> **Loopback (16-23):** <br> 10.10.16.1, 10.10.17.1, 10.10.18.1, 10.10.19.1, 10.10.20.1, 10.10.21.1, 10.10.22.1, 10.10.23.1| **Router ID:** 3.3.3.2 <br> Note that Loopbacks represented connected networks to be shared into OSPF.|
| User1| Desktop (Alpine Linux) | **E3:** 10.10.1.10| **User:** cisco  **Pass:** cisco |


## Preliminary Questions
Before beginning the lab, take some time to think about these questions:
- What are the primary differences between OSPFv2 and OSPFv3?
- How do broadcast multi-access networks differ from point-to-point (P2P) and point-to-multipoint (P2MP) networks in OSPF operation?
- What purpose do areas serve in OSPF design?
- How does OSPF calculate path cost?
- What are the default settings for OSPF (AD, Hello Timer, Dead Timer, Reference BW)?
- Describe the OSPF adjacency process and neighbor states.
- What role does route summarization and filtering play in OSPF design?

## Lab Instructions
Ensure all devices in the topology are booted before continuing. 
Devices should NOT be able to communicate at this point.

### Step 1: Configure OSPFv2 for D1, R1, and R2
In this step, we'll configure OSPFv2 on the listed devices, ensuring all local networks are being shared.
Remember not to touch R3 or D2 just yet!

#### 1.1 Configure OSPFv2 on D1 (Internal Router)
- [ ] Manually set the router ID to 1.1.1.2
- [ ] Set the OSPF cost such that we can distinguish between Gigabit Ethernet and Fast Ethernet interfaces.
- [ ] Enable OSPF on both connected interfaces with the appropriate area **WITHOUT** using the ```network``` command.
- [ ] Set any access ports to NOT advertise OSPF Hello packets.

ß
#### 1.2 Configure OSPFv2 on R1 (Area Border Router)
- [ ] Manually set the router ID to 1.1.1.1
- [ ] Set the OSPF cost such that we can distinguish between Gigabit Ethernet and Fast Ethernet interfaces.
- [ ] Advertise all of R1's connected interfaces into the appropriate area(s) using the ```network``` command.


#### 1.3 Configure OSPFv2 on R2 (Autonomous System Boundary Router)
- [ ] Create and ensure a default route to the Internet exists.
- [ ] Manually set the router ID to 2.2.2.1
- [ ] Set the OSPF cost such that we can distinguish between Gigabit Ethernet and Fast Ethernet interfaces.
- [ ] Enable OSPF on R2s G0/1 and G0/2 interface using a single ```network``` statement.
- [ ] Enable OSPF on R2s Loopback0 interface as a ```point-to-point``` network using either method.
- [ ] Configure R2 to propogate a default route to the Internet.

### Step 2: Verify the OSPFv2 network has been established.
In this step, we will verify that the configured OSPF network was correctly implemented.

#### 2.1 Verify OSPFv2 for D1
- [ ] Examine the active OSPF interfaces and their assigned areas.
- [ ] Is D1 a DR in any of its segments? Why?
- [ ] Examine the neighbor adjacency established by D1.
- [ ] What state are the neighbor adjacencies in?

#### 2.2 Verify OSPFv2 for R1
- [ ] Examine the active OSPF interfaces and their assigned areas.
- [ ] Is R1 a DR in any of its segments? Why? (see solution for caveat!)
- [ ] Examine the neighbor adjacency established by R1.
- [ ] What state are the neighbor adjacencies in?

#### 2.3 Verify OSPFv2 for R2
- [ ] Before proceeding, ensure that the OSPF process has been restarted on both R1 and R2.
- [ ] Examine the active OSPF interfaces and their assigned areas.
- [ ] Is R2 a DR in any of its segments? Why?
- [ ] Examine the neighbor adjacencies established by R1.
- [ ] What state are the neighbor adjacencies in?
- [ ] Verify the gateway of last resort is succesfully being advertised.   
  
### Step 3: Troubleshooting the OSPFv2 network

#### 3.1 Identify the Issue on R3
- [ ] Examine the OSPF process on R3.
- [ ] What state is the OSPF neighbor adjacency in? Why?

#### 3.2 Fix the OSPF process on R3
- [ ] Configure R3 to participate in the OSPF process (there is no need to modify the OSPF process itself).

#### 3.3. Verify OSPFv2 for R3
- [ ] Examine the neighbor adjacencies established by R3.

#### 3.4 Verify network connectivity 
- [ ] Ping from User1 to any of the Loopback "networks" connected to D2.
    - Use ```ping -c n ip-address``` to limit the ping to n attempts on Alpine Linux.

### Step 4: Route Summarization (WORK IN PROGRESS)
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
- Examine the routing table of R3.
