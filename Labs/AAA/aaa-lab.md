# AAA Lab
5.1 Configure and verify device access control (lines and local user auth, AAA)

## Preliminary Questions
Before beginning the lab, take some time to think about these questions:
- What are the advantages of AAA over local line control?
- What does AAA stand for and what does each component do?
- What external servers can we use for AAA? Do we NEED external servers?
- By default, what are the user privileges at each level?
- What are the different encryption standards available to us? Which should we avoid?


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

1.1 Configure a console line password
- Use no encryption
- Set the password to cisco123
- Ensure password is used on console login

1.2 Test console line login
- Logout of the console
- Attempt to enter the console with the configured password.
- Check the running config., how is the password saved? Are we happy with this?

1.3 Encrypt all plain text passwords
- Use service-password encryption to obscure all plaintext passwords.
- Check the running config., what happened to the console password we created?
- What type of encryption are we using?
- Copy the encrypyed password and enter it into this website: https://packetlife.net/toolbox/type7/
- What happened? Are we happy with this?

1.4 Create a local user account
- Configure a local user named ADMIN with Type 5 encrypted password cisco123.
- Set the console to use the newly created local user. What happens to our old password?
- Logout of the console; Attempt to enter the console with the new user account.
- Examine the running config., what does the local user we created look like?
- Copy the encrypted password and enter it into this webiste: https://www.ifm.net.nz/cookbooks/cisco-ios-enable-secret-password-cracker.html
- What happend? Are we happy with this?

1.5 Enhance security with stronger encryption
- Create a new privileged Exec mode password (cisco123) with Type 8 encryption.
- Reconfigure the ADMIN local user to use Type 8 encryption.
- Logout of the console; Attempt to login to the console and enter privileged Exec mode using the newly secured credentials.
- Examine the running config., what does the local user and enable password we created look like?
- Are we inline with the NSA's recommendations now? https://media.defense.gov/2022/Feb/17/2002940795/-1/-1/1/CSI_CISCO_PASSWORD_TYPES_BEST_PRACTICES_20220217.PDF

### Step 2: Terminal Line Protetection and Remote Access
2.1 Configure SSH support
- What must be configured prior to enabling SSH access?
- Set the domain name to CSAP
- Generate a crypto key with a size of 2048. Why does the size of the key matter?
- What versions of SSH are enabled by default? Restrict the device to only support SSHv2. 
- Configure SSH such that the authentication time-out is only 60 seconds or less and a max of 2 attempts is allowed.

2.2 Configure the VTY Lines (all 16)
- Configure the vty lines to only accept SSH (disable Telnet).
- Ensure the local database is queried when remotely accessing the device. 
- Ensure the system disconnects after 3 minutes of inactivity.
- Ensure the system disconnects after 15 minutes regardless of activity.

2.3 Verify SSH operation
- Test the configuration by attempting to SSH from PC1 to R1 and from PC2 to R1. There seems to be some issues with the crypto keys so please use this commmand: 
```ssh -oHostKeyAlgorithms=+ssh-rsa -oKexAlgorithms=+diffie-hellman-group-exchange-sha1 -l admin 192.168.1.1```
- Issue the show ssh command to examine the established sessions.
   
  
### Step 3: Create a new AAA Model
In this step, we'll enabled AAA using local options only for authentication.

3.1 Enable AAA on the device

3.2 Create a method list for login.
- Use the enable password created in step 1.5 as Method 1.
- Ensure the method list is applied to ALL possible login vectors (console, VTY, etc.)

3.3 Create a named method list for remote access login.
- Name the method list VTY-AUTH
- Use only the local user database for authentication.
- Apply the method list to the vty lines.

3.4 Verify AAA operation
- Attempt to SSH from PC1 to R1.
- Were you prompted to login and succesful?

### Step 4: Examine a AAA Model
Make sure you spend some time examining AAA models using RADIUS/TACACS+ groups! 
They are a bit more complicated, so its good to read through examples.
