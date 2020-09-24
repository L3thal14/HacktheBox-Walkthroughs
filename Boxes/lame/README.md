<img src="https://github.com/L3thal14/HacktheBox-Walkthroughs/blob/master/Boxes/lame/screenshots/htblame.png?raw=true"  height="100"  />

#  Lame

**Difficulty** : Easy

**IP Address** : 10.10.10.3

**OS**: Linux 

##  Scanning

We start with scanning the IP for open ports using Nmap using the following command:

`nmap -T4 -p- -A 10.10.10.3`

<img src="https://github.com/L3thal14/HacktheBox-Walkthroughs/blob/master/Boxes/lame/screenshots/nmap_lame.png?raw=true"/>  

We can see from the Nmap Scan results that ports **22,3632** are open.
  

##  Enumeration
Let us try gathering more info about the SSH running on port 22.
<img src="https://github.com/L3thal14/HacktheBox-Walkthroughs/blob/master/Boxes/lame/screenshots/ssh_lame.png?raw=true"/>

This does not give us any kind of information.
But every user has a SSH key which can be used to login to the machine.
If we manage to get access to a normal user's credentials on the machine, we can find the RSA encrypted SSH key and try to escalate root privileges.

##  Searching for Vulnerabilities
From the Nmap Scan results, we observe that the port **3632** running distccd is open and vulnerable.
Fire up **msfconsole** and search for distccd 
  <img src="https://github.com/L3thal14/HacktheBox-Walkthroughs/blob/master/Boxes/lame/screenshots/msf_1_lame.png?raw=true"/>
  
Since, there is only 1 exploit available, let's go ahead with that.
Check the options and set RHOST and LHOST parameters accordingly and run the exploit.
<img src="https://github.com/L3thal14/HacktheBox-Walkthroughs/blob/master/Boxes/lame/screenshots/msf_2_lame.png?raw=true"/>

**Boom!** 
We end up with a command shell.
But, we are not done yet since we are still don't have access to the root user.
Time to search for SSH keys on the machine.
Run the command:
``` cat /root/.ssh/authorized_keys```
<img src="https://github.com/L3thal14/HacktheBox-Walkthroughs/blob/master/Boxes/lame/screenshots/sshkey_lame.png?raw=true"/>

We end up with a SSH key but it is encrypted using RSA Algorithm.
So, for decrypting it, we are going to use a tool which can be downloaded from [here](https://github.com/g0tmi1k/debian-ssh/blob/master/common_keys/debian_ssh_rsa_2048_x86.tar.bz2) .
Download the **tar.bz2** file and open a terminal inside /rsa/2048/ folder.
Now, run the command:
``` grep -lir <RSA Encrypted SSH key> *.pub```
<img src="https://github.com/L3thal14/HacktheBox-Walkthroughs/blob/master/Boxes/lame/screenshots/ssh_decrypted_lame.png?raw=true"/>

The output is ```<Decrypted SSH key>.pub```
Copy this Decrupted SSH key.
Now we are going to plug this in with the SSH command to login to the machine as the **root** user.
<img src="https://github.com/L3thal14/HacktheBox-Walkthroughs/blob/master/Boxes/lame/screenshots/rooted_1_lame.png?raw=true"/>

  **Voila!** We are finally in! and this time around we have root privileges too.
  Now, we need to find the user.txt and root.txt files to get the flags which are usually placed either in **root** or **home** folder.
  <img src="https://github.com/L3thal14/HacktheBox-Walkthroughs/blob/master/Boxes/lame/screenshots/rooted_2_lame.png?raw=true"/>  
  


