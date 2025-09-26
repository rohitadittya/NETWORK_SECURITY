### This document contains commands that help in identifying interfaces, IPs, check open ports and vulnerabilities, monitor real time traffic and check firewall rules.

### 1. Identify Network Interfaces and IP Addresses
#### Goal:
The main goal here is to understand the server's network configuration by finding all the network interfaces and their associated IP addresses.
##### Command:
> ip a
<img width="1082" height="340" alt="image" src="https://github.com/user-attachments/assets/359583c4-b3b9-4459-9ec7-c3bcb3108a90" />

The above output shows the network interfaces (lo, and ens33) and their associated IP addresses

### 2. Check Open Ports
#### Goal:
An attacker starts attacking from a potential entry point, here our goal is to find all the unnecessary open ports that could be a potential entry point for attack.
##### Command:
> ss -tuln
<img width="1062" height="253" alt="image" src="https://github.com/user-attachments/assets/6422dd43-20e3-475e-9abf-7b2f6a1290fb" />

The above output shows all the open ports, and the port 0.0.0.0:631 could be risky because its connected to LAN and any device on same network could access.

### 3. Analyze Network Connections
#### Goal:
Analyze the network connections, find all the processes using this network. This can help to identify unauthorized connections to the server
##### Command:
>sudo lsof -i -P -n
<img width="1081" height="300" alt="image" src="https://github.com/user-attachments/assets/94b02ce7-734f-467b-93ea-1bae0ff3328e" />

### 4. Perform Network Scanning with Nmap
#### Goal:
Find the services that are unintentionally exposed. Find what an attacker might learn about the machine.
##### Command:
>sudo nmap -sS -O localhost
<img width="993" height="340" alt="image" src="https://github.com/user-attachments/assets/4c6998ea-8af6-44b1-b268-1c883ef980eb" />

### 5. Check for Open Ports on the Server's Network
#### Goal:
Find all the unauthorized devices that are connected in the network
##### Command:
>sudo nmap -sP 192.168.1.0/24
<img width="764" height="576" alt="image" src="https://github.com/user-attachments/assets/bd7cf54d-8334-430c-aee6-6b58d68ec966" />

The above output lists all the devices that are connected the local network, it listed my iphone, my roommates phone and laptop.

### 6. Check for Services and Versions
#### Goal:
Find the open ports and determine services running on each port. This could help to fix any software that is outdated or vulnerable.
##### Command:
>sudo nmap -sV localhost
<img width="877" height="187" alt="image" src="https://github.com/user-attachments/assets/53b0425d-2f45-45cc-b338-4dd8cd7eee8d" />

### 7. Identify Potential Vulnerabilities
#### Goal:
We might have installed some software, which could have some security issues. Our goal here is to find those security issues in those installed softwares.
##### Command:
>sudo nmap --script vuln localhost
<img width="790" height="543" alt="image" src="https://github.com/user-attachments/assets/c19cffde-4920-45bc-b565-838270b93e71" />

### 8. Inspect Network Traffic
#### Goal:
Observe real time network traffic and any suspicious activity.
##### Command:
>sudo tcpdump -i eth0
<img width="868" height="531" alt="image" src="https://github.com/user-attachments/assets/95296ee2-fa2e-4fda-bd82-f945bd3fd5c1" />

The above output displays real time network activities and nothing suspicious was detected.

### 9. Monitor Network Connections in Real-Time
#### Goal:
Our goal is to continuously monitor the network, like a service starting and any new connection that has joined the network.
##### Command:
>sudo watch -n 1 netstat -tulnp
<img width="856" height="391" alt="image" src="https://github.com/user-attachments/assets/8da9e4e9-4edd-4605-b77e-5636722be7bf" />

### 10. Check Firewall RulesTime
#### Goal:
See all the ports whether they are open or blocked, mainly our goal is to check the firewall rules that are configured on the server.
##### Command:
>sudo ufw status verbose
<img width="723" height="211" alt="image" src="https://github.com/user-attachments/assets/f24d238a-b077-4da2-9eaf-544231249532" />

The above output shows that there is no firewall setup, so I had enabled the firewall by running `sudo ufw enable`.





