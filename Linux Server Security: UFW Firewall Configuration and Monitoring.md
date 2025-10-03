## Linux Server Security: UFW Firewall Configuration and Monitoring

### I. Enable UFW (Uncomplicated Firewall) 
#### Default UFW Policies -
- Incoming: Denied (blocked) by default.
- Outgoing: Allowed by default.

### 1. Check the status of UFW
Here, we are checking the status of the firewall, whether its active or inactive.

#### Command:
> sudo ufw status
<img width="524" height="51" alt="image" src="https://github.com/user-attachments/assets/d61fbce8-f4e8-4d34-8d2e-dc794bf82d31" />

The above output confirms that the firewall is active.

### 2. Checking if the SSH is allowed
Before enabling the UFW, we are making sure that the SSH is allowed, otherwise it could lock ourselves from the server

#### Command:
> sudo ufw allow 22/tcp

Incase of remote access, we should allow traffic through port 22 before enabling UFW because, our remote connection depends on the port 22. 
If the port 22 is not allowed first before enabling the UFW, then the firewall will block the SSH connection.
So by allowing the 22/tcp port open, it ensures that the SSH connection stays open so it can continue to manage the server after the firewall is turned on.

<img width="580" height="115" alt="image" src="https://github.com/user-attachments/assets/3c46c0f5-bfe3-4332-9d6a-60fa0ea3605b" />

### 3. Check all the open ports
Here we check the other ports that are open that you may need to allow specific connections (80, 443, etc). 
* tuln below is for checking TCP, UDP, and Listening.
* It displays addresses in numeric form.
* Socket Statistics (ss) is a command line utility for checking network statistics.

#### Command:
> sudo ss -tuln

<img width="1679" height="190" alt="image" src="https://github.com/user-attachments/assets/801399c1-b32c-4d44-9da4-31e870c0035b" />

Also, if we are not sure of a particular port, we can use the command **sudo lsof -i:PORT_NUMBER** to see what is running on that port.
<img width="566" height="76" alt="image" src="https://github.com/user-attachments/assets/57ab3a82-1ec6-47fa-8e60-b0807cd3822c" />

### 4. Enable UFW
Now, we are enabling the firewall.

#### Command:
> sudo ufw enable
<img width="490" height="45" alt="image" src="https://github.com/user-attachments/assets/c82f35cf-9520-4411-8ee0-22e3520859e4" />

### 5. Check the firewall status

#### Command:
> sudo ufw status
<img width="539" height="139" alt="image" src="https://github.com/user-attachments/assets/590394f1-3d01-4887-85ad-7396b354bf77" />

### 6. Allowing ports for a Web Server
For a web server, we may need to allow the ports 80 (HTTP), 443 (HTTPS), and 22 (SSH) as these are commonly used ports.

#### Command:
> sudo ufw allow 80/tcp
> sudo ufw allow 443/tcp
<img width="548" height="317" alt="image" src="https://github.com/user-attachments/assets/c2ab650d-d27f-45db-8b1d-995cb7f81e12" />

As we have already allowed port 22, now we are allowing the ports - 80 and 443.

### 7. More detailed version of ufw status
Now, we are using **verbose** command to get more detailed version of ufw status.

#### Command:
> sudo ufw status verbose
<img width="567" height="285" alt="image" src="https://github.com/user-attachments/assets/66588eeb-562b-47fb-8e3e-e280b1126506" />

Now, this displays the following in addition to the regular **ufw status** command,
1. Rules for incoming, outgoing, routed requests
2. Also displays the log level.

### 8. Blacklisting a IP Address using UFW
Now, we are blocking a ex employee's IP Address who is known to wreck havoc.

#### Command:
> sudo ufw deny from 10.0.0.0 to any
<img width="658" height="40" alt="image" src="https://github.com/user-attachments/assets/68c9f62e-a0a1-45e1-bf28-f8c5583d91c4" />

### 9. Allowing a IP Address to access a port
Now, we are allowing a IP Address to access the port 587.
* This is a **Simple Mail Transfer Protocol (SMPT)** port.
* This is used by email clients which allows to send mail.
* Also, the port **587 is authenticated and secured**.

#### Command:
> sudo ufw allow from 192.168.1.50 to any port 587 proto tcp
<img width="869" height="284" alt="image" src="https://github.com/user-attachments/assets/152ec3dc-0ee5-4d00-ab7d-77effba7e4ef" />

### 10. Checking the status again.
<img width="567" height="305" alt="image" src="https://github.com/user-attachments/assets/41ebf33f-73b5-4a58-a569-cf1c8f09cbbf" />

### II. Enable UFW Logging 
### 1. Enable Logging
Now, are enabling UFW Logging. This allows us to monitor the network traffic and detect suspicious activities. It writes the firewall events to the system logs.

#### Command:
> sudo ufw logging on
<img width="533" height="54" alt="image" src="https://github.com/user-attachments/assets/da3f7dc1-417f-457d-ad7b-1da5c7298966" />

### 2. Adjust the log level
We can change the log level based on our requirement. The levels are classified as below.
- low: Minimal logging, mainly for blocked incoming packets.
- medium: Includes blocked incoming packets with additional packet header
details.
- high: Includes all blocked packets and connection information.
- full: Extensive logging of all UFW events

#### Command:
> sudo ufw logging high
<img width="560" height="38" alt="image" src="https://github.com/user-attachments/assets/158ed4e5-481d-4e93-859f-f2defb783034" />

Here, we are setting the log level to **high** to monitor all the blocked packets and connection information.

### 3. Analysing the log entry
It’s useful because it shows who tried to connect (SRC), what service they targeted (DPT), which protocol (PROTO) is used, and that UFW blocked it. Also it helps to monitor, block, and troubleshoot network traffic.

### 4. Fetching the Logs
Now, we are monitoring the log in real time.

#### Command: 
> sudo tail -f /var/log/ufw.log
<img width="830" height="465" alt="image" src="https://github.com/user-attachments/assets/d85274fc-83c1-4392-be35-4c2c6e49685a" />

The logs are stored in /var/log/ufw.log. Adding the OPTION **-f** allows us to monitor in real time

### 5. Filter specific entries.
The **grep** command allows us to filter the specific entries.

#### Command:
> sudo grep 'DENY' /var/log/ufw.log
> sudo grep 'ALLOW' /var/log/ufw.log
<img width="822" height="470" alt="image" src="https://github.com/user-attachments/assets/a43af973-febe-4d68-9efa-761b4a4885e1" />

Here, we dont find any logs for deny because there has not been any connection attempt made after the UFW was enabled.
