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
> sudo suricata -T -c /etc/suricata/suricata.yaml -v

Here we updated default rule path, rules files and interface name
<img width="1636" height="272" alt="image" src="https://github.com/user-attachments/assets/2db48963-d805-4b86-8d09-56122f377c33" />

##### Explaining Flags:
*-T* - Tells Suricata to test the configuration
*-c* - This flag followed by path tells Suricata the path of the configuration file to use
*-v* - Verbose output, it gives more detailed information to the console

### Run Suricata:
Here, we run the Suricata and monitor network traffic and generate alerts.
##### Command:
> sudo systemctl stop suricata
> sudo suricata -i $(ip -br a | awk '$1!="lo"{print $1; exit}') -D

<img width="944" height="120" alt="image" src="https://github.com/user-attachments/assets/d30db1b6-be1c-4784-9132-ed393aacb5bb" />

##### eve.json
This is the main log file for Suricata, and it holds all the events detected by the IDS/IPS in JSON format. This will be used by other tools like Promtail, Loki, or SIEMs to parse and analyze it.

##### Command:
> sudo jq . /var/log/suricata/eve.json

<img width="833" height="439" alt="image" src="https://github.com/user-attachments/assets/9d7e92da-8e0c-4506-a9a9-048a7d1dbc42" />

*Here under event_type field, I was able to see types like **DNS, Stats, and HTTP***

### Part 3 - Loki:
Loki is a log aggregation tool from Grafana Labs that efficiently collects, stores and visualizes application logs. Unlike traditional systems, Loki only indexes log metadata, making it fast and cost effective for large scale logging. It receives logs from agents such as Promtail.

### Preflight setup for Loki:
### 1. Create directories for Loki:
- /etc/loki: where Loki’s configuration file will live.
- /var/lib/loki: Loki’s data directory. Used for storing log chunks and index files.
- {chunks,rules}: creates two subdirectories:
   - chunks/: where Loki stores compressed log data.
   - rules/: where Loki stores alerting or recording rules (not used in this lab but required by the config).
- The -p flag ensures that missing directories are created without errors.

##### Command:
> sudo mkdir -p /etc/loki /var/lib/loki/{chunks,rules}

<img width="851" height="54" alt="image" src="https://github.com/user-attachments/assets/c4cda846-e3d3-411f-a573-b56ca28ca0cd" />

### 2. Create a default Loki configuration file:
<img width="835" height="402" alt="image" src="https://github.com/user-attachments/assets/f9e862ec-1c84-4492-a19c-2054350e73e4" />

<img width="647" height="420" alt="image" src="https://github.com/user-attachments/assets/825dd776-8191-42f9-9e54-74b915cf6507" />

### 3. Fix permessions for Loki to write data.
##### Command:
> sudo chown -R 10001:10001 /var/lib/loki
> sudo chmod -R u+rwX /var/lib/loki

<img width="702" height="52" alt="image" src="https://github.com/user-attachments/assets/35e7c37f-f8bf-4e15-bf07-d656520fea8f" />

### 4. Run Loki
##### Command:
> sudo docker run -d --name loki -p 3100:3100  -v /etc/loki:/etc/loki  -v /var/lib/loki:/var/lib/loki  grafana/loki:2.9.8 -config.file=/etc/loki/loki-config.yml

<img width="1714" height="45" alt="image" src="https://github.com/user-attachments/assets/5412b54b-f9fa-4bea-b489-b31da95037fe" />

Now, Loki exposes *Port 3100* and the api */loki/api/v1/push* on port 3100 receives the log data.

### Part 4: Run Promtail
Promtail collects logs from the system and sends them to Loki, which works hand-in-hand with Loki. Its main job is to find, read and forward the logs.

### 1. Create Promtail folders:
- /etc/promtail: We store the Promtail’s config file.
- /var/lib/promtail: Where Promtail keeps its positions file, a bookmark so it knows how far it has read in each log and does not resend lines.

##### Command:
> sudo mkdir -p /etc/promtail /var/lib/promtail

<img width="747" height="65" alt="image" src="https://github.com/user-attachments/assets/a24db319-e303-44d6-b126-a546ee21bb5b" />

### 2. Write the promtail config file:
<img width="849" height="565" alt="image" src="https://github.com/user-attachments/assets/a791a5ce-d5df-45bc-ab3f-7e8944258711" />

### 3. Run the Promtail container:
##### Command:
> docker run -d --name promtail -p 9080:9080 \
 -v /etc/promtail:/etc/promtail \
 -v /var/log/suricata:/var/log/suricata:ro \
 -v /var/lib/promtail:/var/lib/promtail \
 grafana/promtail:2.9.8 \
 -config.file=/etc/promtail/promtail-config.yml

<img width="742" height="333" alt="image" src="https://github.com/user-attachments/assets/c1a2eb9c-d133-437b-ae2c-5cb223c218a6" />

##### What role does Promtail play compared to Loki? 
- Promtail is a log collector that finds, reads and ships logs to Loki, whereas Loki is the central log database that stores, indexes and lets us search log data.

##### Why does Promtail track a “position file”? What problem does it solve?
- Promtail tracks a position file which acts as a bookmark, which remember how much of a log file it has already sent, so it doesnot resend old lines if restarted. This helps in solving the problem of duplicate logs.

### Part 5 - Install LogCLI and Test Queries:
It is a Grafana Loki's CLI tool. It allows us to query, view and analyze logs directly from the terminal.

### 1. Download and install LogCLI:
##### Command:
> curl -L https://github.com/grafana/loki/releases/download/v2.9.8/logclilinux-arm64.zip -o /tmp/logcli.zip
sudo unzip -o /tmp/logcli.zip -d /usr/local/bin
sudo mv /usr/local/bin/logcli-linux-arm64 /usr/local/bin/logcli
sudo chmod +x /usr/local/bin/logcli
logcli --version

The above command downloads the logclie as a ZIP file from Github, unzips to /usr/local/bin and renames the file to logcli. Also, makes it an executable file.
Finally, it checks if its working by printing the version.

<img width="879" height="359" alt="image" src="https://github.com/user-attachments/assets/6b3ac475-1a89-4c08-b63f-12e6d18d1fbe" />

### 2. Verify connectivity and list available lables:
Test if the LogCLI can connect to the Loki server running on localhost:3100.

##### Command:
> logcli labels --addr=http://localhost:3100

<img width="1070" height="52" alt="image" src="https://github.com/user-attachments/assets/ef2c84cd-aae8-49fe-af09-b68eabef8bdc" />

### 3. Query recent logs:
Using Loki to fetch upto 10 recent log entries at localhost:3100 from Suricata job.
##### Command:
> logcli query --addr=http://localhost:3100 --limit=10 '{job="suricata"}'

<img width="1599" height="43" alt="image" src="https://github.com/user-attachments/assets/efdb15d4-0756-469c-aaec-640c50f6ec4e" />

The following labels - *job, instance, host, level* can be found in the logs. Label differ from full text index as Labels index log metadata for fast filtering, whereas full text indexes index the entire log content for searching specific words.

### Part 6 - Generate Alerts and Analyze:
Verify everything is working fine by triggering a test alert.

### 1. Add a custom Suricata rule to guarantee an alert:
##### Command:
> echo 'alert http any any -> any any (msg:"LAB UA hit"; http.user_agent;
content:"CPS-NETSEC-LAB"; sid:9900001; rev:1;)' \
| sudo tee -a /etc/suricata/rules/local.rules

<img width="980" height="102" alt="image" src="https://github.com/user-attachments/assets/704089c9-3492-4655-b056-727332ecd7cb" />

### 2. Restart Suricata to load the new rule:
##### Command:
> sudo systemctl restart suricata

### 3. Trigger the alert:
Now we send an HTTP request with the User-Agent header "CPS-NETSEC-LAB".
##### Command:
> sudo suricata -T -c /etc/suricata/suricata.yaml -v

### 4. Query alerts in Loki:
##### Command:
> logcli query --addr=http://localhost:3100 --limit=50 \ '{job="suricata"} |= "event_type\":\"alert\"" |json | line_format " {{.alert.signature}}"'
 
<img width="714" height="218" alt="image" src="https://github.com/user-attachments/assets/5e7f0fcd-bc3c-434a-a5fa-2794dc274052" />
<img width="780" height="100" alt="image" src="https://github.com/user-attachments/assets/02c03f26-d058-4269-ac45-5165e439c078" />

Here, by doing above it helped connect to Loki and searched through all Suricata logs, displaying the actual alerts. Then it parsed the alert JSON and printed the alert signature. The *LAB UA hi* alert message was seen.

### Part 7 - Correlation challenge:
Finding the top source IPs in last 5 minutes:
##### Command: 
> logcli query --addr=http://localhost:3100 --limit=1000 --since=5m \
'{job="suricata"} |= "event_type\":\"alert\"" | json | line_format "
{{.src_ip}} "' \
 | sort | uniq -c | sort -nr | head

By running above, it pulled all the Suricata logs from last 5mins and printed the SOURCE IP address in a sorted order with count of each time the IP appeared.

Question 9: It shows how SIEMs correlate events by a common field and aggregate them into counts for pattern detection
Question 10: A SOC can identify top offending IPs to prioritize investigations and response to active threats.

### Part 8 - Create and Test Your Own custom rule:
Custom Alert:
> alert http any any -> any any (msg:"Rohit Bad Host Test"; content:"badhost.com"; http_host; sid:9901234; rev:1;)

<img width="639" height="257" alt="image" src="https://github.com/user-attachments/assets/cd50583b-fd99-4cc6-88d9-349871e19b14" />
<img width="772" height="164" alt="image" src="https://github.com/user-attachments/assets/6773dc3c-f0e9-44a6-949c-f24c3fcc8b07" />

1. What condition did your rule detect?
   The rule detects any HTTP traffic where the HTTP Host header is "badhost.com"
3. How did you test and confirm that it triggered correctly?
   I ran curl -A "TEST" http://badhost.com/ where the user agent is TEST and it was logged as expected
4. How would you modify your rule to make it more specific (to reduce false positives)?
   We can restric the rule to certain IPs/ Ports, use exact content match.
5. Why is fine-tuning rules important in real-world intrusion detection?
   It can reduce the false positives, minimize resource wastage, improves the accuracy of detection.


### Summary:
This lab helped me in setting up a lightweight SIEM/ IDPS pipeline using Suricata, Promtail, Loki and LogCLI to detect custom network threats, centralize logs and analyze alerts. Also, I tested custom IDS rules for practical security event detection.

