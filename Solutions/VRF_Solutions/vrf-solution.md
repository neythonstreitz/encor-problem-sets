# VRF Lab
2.2 Configure and verify data path virtualization technologies (2.2a VRF)

## Preliminary Questions
Before beginning the lab, take some time to think about these questions:
- What is the purpose of VRF?
    - VRF allows us to use the same physical hardware but separate routing table instances, so we can keep customers or specific groups separate.
- What is the difference between VRF and VRF-lite?
    - Technically, VRF-lite refers to the way VRF is used. Specifically, VRF-lite refers to the ability to separate routing interfaces and tables. VRF can alternatively be configured with MPLS and can be shared across multiple sites, aka not very lite!
- Describe common use cases for VRF technology.
    - Separating customer instances for a Service Provider. Separating organizations within a business for auditing purposes.


## Lab Introduction
In this lab, you'll practice configuring and examining VRF configurations on a router to separate customer traffic. Note that we will be implementing VRF-lite, although we will refer to it as VRF!

## Lab Instructions
Before starting, ensure lab environment has succesfully been imported and devices are up and running.

### Step 1: Create VRF-lite Instances
In this step, we'll create VRF instances for the two customers in our environment.

1.1 Create Customer_A VRF
- Do not configure anything yet!
```
ISP1(config)# ip vrf Customer_A
ISP1(config-vrf)# exit
```

1.2 Create Customer_B VRF
- Do not configure anything yet!
```
ISP1(config)# ip vrf Customer_B
ISP1(config-vrf)# exit
```

1.3 Verify VRF creation
```
ISP1# show ip vrf
  Name                             Default RD            Interfaces
  Customer_A                       <not set>             
  Customer_B                       <not set> 
```
We can see the VRFs were created but no interfaces set yet.

### Step 2: AssignLoopback and Sub-interface interfaces

2.1 Create Loopback0
- Assign to the Customer_A VRF
- Configure with 1.1.1.1/32
- Ensure interface is turned on

```
ISP1(config)# int l0
ISP1(config-if)# ip vrf forwarding Customer_A
ISP1(config-if)# ip add 1.1.1.1 255.255.255.255
ISP1(config-if)# exit
```

2.2 Create Loopback1
- Assign to the Customer_B VRF
- Configure with 2.2.2.2/32
- Ensure interface is turned on

```
ISP1(config)# int l1
ISP1(config-if)# ip vrf forwarding Customer_B
ISP1(config-if)# ip add 2.2.2.2 255.255.255.255
ISP1(config-if)# exit
```

2.3 Create sub-interface e0/3.100
- Enable dot1q encapsulation for VLAN 100
- Assign to the Customer_A VRF
- Configure with 192.168.0.1/24
- Ensure interface is turned on

```
ISP1(config)# int g0/3.100
ISP1(config-subif)# ip vrf forwarding Customer_A
ISP1(config-subif)# ip add 192.168.0.1 255.255.255.0
ISP1(config-subif)# exit
```

2.4 Create sub-interface e0/3.200
- Enable dot1q encapsulation for VLAN 200
- Assign to the Customer_B VRF
- Configure with 192.168.0.1/24
- Ensure interface is turned on

```
ISP1(config-subif)# int g0/3.200 
ISP1(config-subif)# ip vrf forwarding Customer_B
ISP1(config-subif)# ip add 192.168.0.1 255.255.255.0
ISP1(config-subif)# exit
```

2.5 Verify VRF Assignment

```
ISP1#show ip vrf
  Name                             Default RD            Interfaces
  Customer_A                       <not set>             Lo0
                                                         Gi0/3.100
  Customer_B                       <not set>             Lo1
                                                         Gi0/3.200
```
  
### Step 3: Routing Tables

3.1 Examine the global routing table on ISP1
- What routes are there?
    - There are no routes in the global table.
- Is this correct behavior?
    - Yes!

3.2 Examine Customer_A VRF routing table on ISP1
- What routes are there?
    - The route to 1.1.1.1 and the connected network 192.168.0.0/24 with its connected interface 192.168.0.1/32 on g0/3.100
- Is this correct behavior?
    - Yes this is perfect! We have the Loopback network that we want and the route to the 192.168.0.0/24 is through the subinterface.

3.3 Examine Customer_B VRF routing table on ISP1
- What routes are there?
    - The route to 2.2.2.2 and the connected network 192.168.0.0/24 with its connected interface 192.168.0.1/32 on g0/3.200
- Is this correct behavior?
    - Same as before!

### Step 4: Test VRF implementation

4.1 Create a default route on SW1
- Gateway is ISP1.
    - ```SW1(config)#ip route 0.0.0.0 0.0.0.0 192.168.0.1```

4.2 Create a default route on SW2
- Gateway is ISP1.
    - ```SW2(config)#ip route 0.0.0.0 0.0.0.0 192.168.0.1```

4.3 Pinging from SW1
- Ping ISP1s Loopback0. Is it succesful? Are we happy?
    - It is succesful. Very happy!
- Ping ISP1s Loopback1. Is it succesful? Are we happy?
    - It is unallowed. Happy again! 

4.4 Ping from SW2
- Ping ISP1s Loopback0. Is it succesful? Are we happy?
    - It is unallowed. Happy!
- Ping ISP1s Loopback1. Is it succesful? Are we happy?
    - It is succesful. Perfect!

4.5 Ping from SW1 to SW2
- Customer_A and Customer_B both reside in the same network...
- Should this ping work?
    - It should NOT work since we have them split into separate VLANs and our router separates them into VRFs so there is no inter-vlan routing occuring either!