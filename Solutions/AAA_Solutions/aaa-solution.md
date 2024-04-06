# AAA Lab
5.1 Configure and verify device access control (lines and local user auth, AAA)

## Preliminary Questions
Before beginning the lab, take some time to think about these questions:
- What are the advantages of AAA over local line control?
    - Centralized database of users means you don't have to create a user account on each device. Additionally adds more rigorous security with RADIUS/TACACS+.
- What does AAA stand for and what does each component do?
    - Authentication: Who are you?, Authorization: Are you allowed to do that?, Accounting: I'm watching you!
- What external servers can we use for AAA? Do we NEED external servers?
    - RADIUS or TACACS+, not needed for AAA as we can use local database.
- By default, what are the user privileges at each level?
- What are the different encryption standards available to us? Which should we avoid?
    - Avoid Type 0 (no encryption), Type 5 (MD5), and Type 7 (Vigenere). Both Type 8 and 9 are safe, but NSA only recommends Type 8.


## Lab Introduction
In this lab, you'll practice configuring and examining AAA configurations on a router. Unfortunately, we can only truly test local configuration since we can't set up an external server (I'm too lazy to figure it out...)!

## Lab Instructions
Before starting, ensure lab environment has succesfully been imported and router has a startup config. 
Make sure to setup the PC's with the proper IP addresses, since they may not be configured correctly on startup.
```
sudo hostname PC1
sudo ifconfig eth0 192.168.1.50 netmask 255.255.255.0

sudo hostname PC2
sudo ifconfig eth0 192.168.1.49 netmask 255.255.255.0
```

Verify connectivity by pinging from PC1 and PC2 to R2, and pinging between PC1 and PC2. Careful, the ping command will run continuously if you don't specify a count (-c)!

```
ping -c 4 192.168.1.1
```


### Step 1: Configure, Verify, and Qualify Local User Password Protection
In this step, we'll practice configuring passwords of different encryption types for various lines on the device.
NOTE: Do not save the running config to the startup config, unless you've thoroughly tested the passwords! Basically, don't lock yourself out!

1.1 Configure a console line password on R1
- Use no encryption
- Set the password to cisco123
- Ensure password is used on console login

```
R1(config)#line con 0
R1(config-line)#password cisco123
R1(config-line)#login
```

1.2 Test console line login on R1
- Logout of the console
- Attempt to enter the console with the configured password.
- Check the running config., how is the password saved? Are we happy with this?
    - The password is saved as cleartext! This is not very secure, we are not happy...

```
R1#show run | begin line con
line con 0
 exec-timeout 0 0
 password cisco123
 logging synchronous
 login
```

1.3 Encrypt all plain text passwords on R1
- Use service password-encryption to obscure all plaintext passwords.
    - ```R1(config)#service password-encryption```
- Check the running config., what happened to the console password we created?
    - It is now encrypted! -> ```password 7 030752180500701E1D```
- What type of encryption are we using?
    - Type 7 encryption aka Vigenere.
- Copy the encrypyed password and enter it into this website: https://packetlife.net/toolbox/type7/
- What happened? Are we happy with this?
    - It is easily cracked! Not very happy...

1.4 Create a local user account on R1
- Configure a local user named ADMIN with Type 5 encrypted password cisco123.
    - ```R1(config)#username ADMIN secret cisco123``` or ```R1(config)#username ADMIN algorithm-type md5 secret cisco123```
    - Please note that by default, "username ADMIN secret cisco123" should generate a Type 5 password, but on this version of IOSv it now seems to default to 9, so we have to manually specify!
- Set the console to use the newly created local user. What happens to our old password?
    - ```R1(config)#line con 0```
      ```R1(config-line)#login local```
- Logout of the console; Attempt to enter the console with the new user account.
- Examine the running config., what does the local user we created look like?
    - ```username ADMIN secret 5 $1$zY2k$9dUAiEKHIkgPU06aaJJ/.0```. We are using Type 5 encryption! 
- Copy the encrypted password and enter it into this webiste: https://www.ifm.net.nz/cookbooks/cisco-ios-enable-secret-password-cracker.html
- What happend? Are we happy with this?
    - Still crackable... need something stronger!

1.5 Enhance security with stronger encryption on R1
- Create a new privileged Exec mode password (cisco123) with Type 8 encryption.
    - ```R1(config)#enable algorithm-type sha256 secret cisco123 ```
- Reconfigure the ADMIN local user to use Type 9 encryption.
    - ```R1(config)# username ADMIN algorithm-type scrypt secret cisco123```
- Logout of the console; Attempt to login to the console and enter privileged Exec mode using the newly secured credentials.
- Examine the running config., what does the local user and enable password we created look like?
    - ```enable secret 8 $8$Fe89cuMvLhhBMP$3ZX7phBWvbfcrRd4LHTdrhDcOC96vQnNKevkbMZvMm2``` and ```username ADMIN secret 9 $8$RMTEaLwPGkHmEP$mzP.ctRQL79pMUoFqnXkK9b.FvuX.gU6eCcO1ESgTew```
- Are we inline with the NSA's recommendations now? https://media.defense.gov/2022/Feb/17/2002940795/-1/-1/1/CSI_CISCO_PASSWORD_TYPES_BEST_PRACTICES_20220217.PDF
    - Both passwords are encrypted well, but only Type 8 is approved by NIST.
    
### Step 2: Terminal Line Protetection and Remote Access
2.1 Configure SSH support on R1
- What must be configured prior to enabling SSH access?
    - Hostname, domain name, correct time, and local username.
- Set the domain name to CSAP
    - ```ip domain-name CSAP```
- Generate a crypto key with a size of 2048. Why does the size of the key matter?
    - ```crypto key generate rsa modulus 2048```. This means the value used to calculate our keys (the modulus) will be between 2^2047 and 2^2048 and the longer, the more secure, but the more computation it takes to create!
- What versions of SSH are enabled by default? Restrict the device to only support SSHv2. 
    - By default we should be in SSH v1.99 which means we can use both SSHv1 and SShv2. We can change this by running: ```ip ssh version 2```.
- Configure SSH such that the authentication time-out is only 60 seconds or less and a max of 2 attempts is allowed.
    - ```R1(config)#ip ssh time-out 60```
      ```R1(config)#ip ssh authentication-retries 2```

2.2 Configure the VTY Lines on R1
- Configure the vty lines to only accept SSH (disable Telnet).
    - ```R1(config)#line vty 0 15```
      ```R1(config-line)#transport input ssh```
- Ensure the local database is queried when remotely accessing the device. 
    - ```R1(config-line)#login local```
- Ensure the system disconnects after 3 minutes of inactivity.
    - ```R1(config-line)#exec-timeout 3 0```
- Ensure the system disconnects after 15 minutes regardless of activity.
    - ```R1(config-line)#absolute-timeout 15```

2.3 Verify SSH operation on PC1 and PC2
- Test the configuration by attempting to SSH from PC1 to R1 and from PC2 to R1.
There seems to be some issues with the crypto keys so please use this commmand: ```ssh -oHostKeyAlgorithms=+ssh-rsa -oKexAlgorithms=+diffie-hellman-group-exchange-sha1 -l admin 192.168.1.1```
- Issue the show ssh command to examine the established sessions.
    - ```show ssh```. We can see both of the SSH connections we established from PC1 and from PC2!    
  
### Step 3: Create a new AAA Model
In this step, we'll enabled AAA using local options only for authentication.

3.1 Enable AAA on the device
- ```R1(config)# aaa new-model```

3.2 Create a method list for login.
- Use the enable password created in step 1.5 as Method 1.
- Ensure the method list is applied to ALL possible login vectors (console, VTY, etc.)
```R1(config)# aaa authentication login default enable```

3.3 Create a named method list for remote access login.
- Name the method list VTY-AUTH
- Use only the local user database for authentication.
- Apply the method list to the vty lines. 
```
R1(config)#aaa authentication login VTY-AUTH local
R1(config)#line vty 0 15
R1(config-line)#login authentication VTY-AUTH
```

3.4 Verify AAA operation
- Attempt to SSH from PC1 to R1.
    - ```ssh -oHostKeyAlgorithms=+ssh-rsa -oKexAlgorithms=+diffie-hellman-group-exchange-sha1 -l admin 192.168.1.1```
- Were you prompted to login and succesful?
    - Yup!

### Step 4: Examine a AAA Model
Make sure you spend some time examining AAA models using RADIUS/TACACS+ groups! 
They are a bit more complicated, so its good to read through examples.
