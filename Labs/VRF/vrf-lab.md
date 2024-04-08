# VRF Lab
2.2 Configure and verify data path virtualization technologies (2.2a VRF)

## Preliminary Questions
Before beginning the lab, take some time to think about these questions:
- What is the purpose of VRF?
- What is the difference between VRF and VRF-lite?
- Describe common use cases for VRF technology.


## Lab Introduction
In this lab, you'll practice configuring and examining VRF configurations on a router to separate customer traffic. Note that we will be implementing VRF-lite, although we will refer to it as VRF!

## Lab Instructions
Before starting, ensure lab environment has succesfully been imported and devices are up and running.

### Step 1: Create VRF-lite Instances
In this step, we'll create VRF instances for the two customers in our environment.

1.1 Create Customer_A VRF
- Do not configure anything yet!

1.2 Create Customer_B VRF
- Do not configure anything yet!

1.3 Verify VRF creation

### Step 2: Assign Loopback and Sub-interface interfaces

2.1 Create Loopback0
- Assign it to the Customer_A VRF
- Configure it with the address 1.1.1.1/32
- Ensure interface is turned on

2.2 Create Loopback1
- Assign it to the Customer_B VRF
- Configure it with the address 2.2.2.2/32
- Ensure interface is turned on

2.3 Create sub-interface g0/3.100
- Assign it to the Customer_A VRF
- Enable dot1q encapsulation for VLAN 200
- Configure it with the address 192.168.0.1/24
- Ensure interface is turned on

2.4 Create sub-interface g0/3.200
- Assign it to the Customer_B VRF
- Enable dot1q encapsulation for VLAN 200
- Configure it with the address 192.168.0.1/24
- Ensure interface is turned on

2.5 Verify VRF Assignment

  
### Step 3: Routing Tables

3.1 Examine the global routing table
- What routes are there?
- Is this correct behavior?

3.2 Examine Customer_A VRF routing table
- What routes are there?
- Is this correct behavior?

3.3 Examine Customer_B VRF routing table
- What routes are there?
- Is this correct behavior?

### Step 4: Test VRF implementation

4.1 Create a default route on SW1
- Gateway is ISP1.

4.2 Create a default route on SW2
- Gateway is ISP1.

4.3 Pinging from SW1
- Ping ISP1s Loopback0. Is it succesful? Are we happy?
- Ping ISP1s Loopback1. Is it succesful? Are we happy?

4.4 Ping from SW2
- Ping ISP1s Loopback0. Is it succesful? Are we happy?
- Ping ISP1s Loopback1. Is it succesful? Are we happy?

4.5 Ping from SW1 to SW2
- Customer_A and Customer_B both reside in the same network...
- Should this ping work?
