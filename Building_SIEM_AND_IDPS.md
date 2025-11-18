### In this lab, we will build a lightweight SIEM and Intrusion Detection/Prevention System (IDPS) using tools like Suricata for detecting and logging network eventsv, Promtail for log shipping, Loki for centralized log storage, and LogCLI for querying and correlation of alerts. 

### Setup and Tasks
### Part 1 
### 1. Prepare System
Here we will be installing couple of tools like **curl, jq, unzip and docker**
##### Command:
> sudo apt update && sudo apt upgrade -y
> sudo apt -y install curl jq unzip

<img width="980" height="697" alt="image" src="https://github.com/user-attachments/assets/060149c5-84d5-4db6-aa6d-a4745035f9ef" />

### 2. Install Docker
Here we install the Docker
##### Command:
> curl -fsSL https://get.docker.com | sudo sh

<img width="1790" height="355" alt="image" src="https://github.com/user-attachments/assets/bf5d232e-3759-4465-8237-64d6f08d4392" />

### 3. Add the user to the Docker Group
Here, we add the current user to the Docker group so that we can run the docker without sudo each time.
##### Command:
> sudo usermod -aG docker "$USER"
> newgrp docker

<img width="623" height="40" alt="image" src="https://github.com/user-attachments/assets/445a6790-49ac-403d-8b22-9234a695243f" />

### 4. Start the Docker Service
Here, we enable and start the docker service
##### Command:
> sudo systemctl enable --now docker
> docker --version

<img width="922" height="116" alt="image" src="https://github.com/user-attachments/assets/171caad2-4f03-4e03-8a9e-3c26cf91c36f" />

### Part 2 - Suricata:
Suricate is a high performance Network IDS, IPS and Network Security Monitoring Engine that watches network traffic and alerts on suspicious activity. It scans and detects attacks, malware. Surricate generally offers better performance and easier SIEM integration while Snort is highly customizable and compatible.

### Preflight Setup for Suricate:
Here, we are making sure that default rules are valid and correct file paths are configured for Suricate.
##### Command:
> sudo apt -y install suricata
> sudo apt -y install suricata-update
> sudo suricata-update

<img width="1395" height="229" alt="image" src="https://github.com/user-attachments/assets/41cddb1a-7859-4664-ad10-689395e21efa" />
<img width="690" height="149" alt="image" src="https://github.com/user-attachments/assets/c4da5bf9-3625-41b3-8e57-382f2bc514c4" />
<img width="1119" height="522" alt="image" src="https://github.com/user-attachments/assets/6f109c50-b66d-4fb2-8ae0-85a1e11f4392" />

### Noting down the interface name for future use:
<img width="698" height="68" alt="image" src="https://github.com/user-attachments/assets/c8476185-a70d-48a6-8464-51671ab6b145" />

### Create Directory and File for our custom rules:
##### Command:
> sudo mkdir -p /etc/suricata/rules
> sudo touch /etc/suricata/rules/local.rules

<img width="717" height="66" alt="image" src="https://github.com/user-attachments/assets/14ccab1e-fe95-41b8-be34-d16c36587fd3" />

### Update the suricate.yaml file to the correct rule path
##### Command:
> sudo nano /etc/suricata/suricata.yaml


