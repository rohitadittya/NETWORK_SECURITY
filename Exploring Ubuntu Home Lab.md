### This document contains commands that help in identifying interfaces, IPs, check open ports and vulnerabilities, monitor real time traffic and check firewall rules.

### 1. Identify Network Interfaces and IP Addresses
#### Goal:
The main goal here is to understand the server's network configuration by finding all the network interfaces and their associated IP addresses.
##### Command:
> ip a


The above output shows the network interfaces (lo, and ens33) and their associated IP addresses

### 2. Check Open Ports
#### Goal:
An attacker starts attacking from a potential entry point, here our goal is to find all the unnecessary open ports that could be a potential entry point for attack.
##### Command:
> ss -tuln


The above output shows all the open ports, and the port 0.0.0.0:631 could be risky because its connected to LAN and any device on same network could access.

### 3. Analyze Network Connections
#### Goal:
Analyze the network connections, find all the processes using this network. This can help to identify unauthorized connections to the server
##### Command:
>sudo lsof -i -P -n


### 4. Perform Network Scanning with Nmap
#### Goal:
Find the services that are unintentionally exposed. Find what an attacker might learn about the machine.
##### Command:
>sudo nmap -sS -O localhost


### 5. Check for Open Ports on the Server's Network
#### Goal:
Find all the unauthorized devices that are connected in the network
##### Command:
>sudo nmap -sP 192.168.1.0/24

The above output lists all the devices that are connected the local network, it listed my iphone, my roommates phone and laptop.

### 6. Check for Services and Versions
#### Goal:
Find the open ports and determine services running on each port. This could help to fix any software that is outdated or vulnerable.
##### Command:
>sudo nmap -sV localhost


### 7. Identify Potential Vulnerabilities
#### Goal:
We might have installed some software, which could have some security issues. Our goal here is to find those security issues in those installed softwares.
##### Command:
>sudo nmap --script vuln localhost

### 8. Inspect Network Traffic
#### Goal:
Observe real time network traffic and any suspicious activity.
##### Command:
>sudo tcpdump -i eth0

The above output displays real time network activities and nothing suspicious was detected.

### 9. Monitor Network Connections in Real-Time
#### Goal:
Our goal is to continuously monitor the network, like a service starting and any new connection that has joined the network.
##### Command:
>sudo watch -n 1 netstat -tulnp

### 10. Check Firewall RulesTime
#### Goal:
See all the ports whether they are open or blocked, mainly our goal is to check the firewall rules that are configured on the server.
##### Command:
>sudo ufw status verbose

The above output shows that there is no firewall setup, so I had enabled the firewall by running `sudo ufw enable`.





