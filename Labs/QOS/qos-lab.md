# QOS Lab
1.5 Interpret wired and wireless QoS configurations (components and policy)

## Preliminary Questions
Before beginning the lab, take some time to think about these questions:
- What is the purpose of QoS?
- What are the different methods for tagging packets with QoS marks? (L2 and L3)
- What are the different standardized encodings for DSCP traffic?
- Describe the process of queueing and scheduling.
- What are the differences between shaping and policing?


## Lab Introduction
In this lab, you'll practice configuring and examining QoS configurations on a router. Unfortunately, you won't be able to test the QoS working on live traffic... :/

## Lab Instructions
Before starting, ensure lab environment has succesfully been imported and router has a startup config. 

### Step 1: Create class-maps for interesting traffic
In this step, we'll create multiple class maps for the traffic we are interested in implementing QoS for.

1.1 Email
  - Ensure the EMAIL class map matches any relevant email protocols (pop3, smtp, imap, exchange)

1.2 Voice
  - Ensure the VOICE class map matches the relevant voice protocol. HINT: Once SIP establishes a connection, what takes over?
  - Careful that video traffic is not captured by the class map!

1.3 Scavenger
  - Ensure the SCAVENGER class map prevents users from torrenting on the network. HINT: Look for peer-to-peer (p2p) file sharing clients...

1.4 Verify class-maps
  - Examine the class-maps that you have created using the appropriate show command.
  - How many class-maps are there in total?
  - What happens to traffic that is not matched by our custom class-maps?

### Step 2: Create policy-maps to configure class behavior
In this step, create a policy-map called CAMPUS with the behaviors listed below.

2.1 Email
  - Configure email traffic to conform to the following parameters:
    - Not priority traffic
    - Guarentee at least 512 Kb
    - Use WRED as a drop policy based on DSCP values.
    - Enable explicit congestion notifications.
   
2.2 Voice
  - Configure voice traffic to conform to the following parameters:
    - Priority traffic
    - Should NOT consume more than 256 Kb

2.3 Scavenger
  - Configure voice traffic to conform to the following paremeters:
    - Not priority traffic
    - Police above 128 Kb, dropping non-conformant traffic.
   
  
### Step 3: Create service-map to apply QoS policy
In this step, apply QoS policy to an interface.

3.1 Apply the CAMPUS policy-map to the GigabitEthernet0/0 interface.
  - Ensure QoS is occuring outbound on the interface.

### Step 4: Verify QoS Policy
In this step, ensure all configured QoS was succesfully applied.

4.1 Examine the policy-map on the GigabitEthernet0/0 interface.
  - Investigate how configurations made earlier show up in the show command. How do you know that a traffic class is using WRED, ENC, Priority queue, Policing?
  - What is the commited burst rate of Voice traffic?
  - What is the commited burst rate of Scavenger traffic?
