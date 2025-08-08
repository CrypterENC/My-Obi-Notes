[[WireShark]]
#WireShark-Lab-1
## Getting Started with Wireshark - Lab 1  
  
*In this intro lab we will get our hands on Wireshark and try out some basic functions. Don't worry if you don't know the answers right away, part of this exercise is to help you dig for the answers.*
  
**Here are the questions that we will answer:**  
  
### 1. How many packets were captured in this file ?

-  You have *2 ways* to know that : 

1. **First Way :** 

	1.  Goto -> **Statistics tab** -> **Capture File Properties.**
	
		![[Pasted image 20250807002103.png]]
	    ![Capture File Properties](https://github.com/CrypterENC/My-Obi-Notes/blob/main/IMG/Pasted%20image%2020250807002103.png)

	3.  So In this `lab1.pcapng` we got **268** packets.

2. **Second Way :** 

	1.  In the **Main Wireshark Window** `-->` **Look at the bottom.**

		![[Pasted image 20250807002626.png]]
        ![Main Wireshark Window-Look at the bottom](https://github.com/CrypterENC/My-Obi-Notes/blob/main/IMG/Pasted%20image%2020250807002626.png)


### 2. How many DNS packets are in the file?  

- You can use `dns` filter the search box.

	![[Pasted image 20250807205047.png]]

### 3 Look at packet number 5 - what is the destination IP address in this packet ?

- **Destination Ip address is :**  `104.16.143.237`

	![[Pasted image 20250807205427.png]]
### 4. What is the destination TCP port in this same packet?  

-  **Check the Info Tab.**

-  Destination TCP port is **80**

	![[Pasted image 20250807205427.png]]

### 5. What application typically uses this port ? 

- **80** `-->` **HTTP**
### 6. What TCP flag is set in this packet ?  

-  **Same check the detailed transmission window.**

- **SYN**

	![[Pasted image 20250807230955.png]]
### 7. What is the frame number of the next packet in this TCP conversation ?  


-  To get the next frame number of the packet in this TCP Conversation, we can use Conversation Filter

	 ![[Pasted image 20250807233934.png]]

-  So 9th packet this next packet in this TCP conversation.

### 8. Can you set a filter for this conversation ? How many packets do you get ?

-  **To set a filter for this conversation** `-->` **right click on the packet**`-->` **select Conversation Filter** `-->` **and select the TCP protocol.**

	![[Pasted image 20250807231125.png]]

	![[Pasted image 20250808153228.png]]

	- **We the answer to the question** " *How many packets do you get ?* " is **3 packets**
