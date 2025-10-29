### This document contains steps to install and configure Snort.

### Step 1: Update the system
##### Command:
> sudo apt update
> sudo apt upgrade -y

<img width="816" height="210" alt="image" src="https://github.com/user-attachments/assets/ca39dca4-d4f5-4673-abe6-d37d0bf849e9" />


### Step 2: Install Snort
##### Command:
> sudo apt install snort -y

<img width="1461" height="233" alt="image" src="https://github.com/user-attachments/assets/3a3ade38-2108-40f2-b895-74d85a372b02" />

### Step 3: Configure Snort
##### Command:
> sudo nano /etc/snort/snort.conf

### Step 4: Update and Manage Snort Rules
##### Command:
> sudo wget https://www.snort.org/downloads/community/community-rules.tar.gz
> sudo tar -xvzf community-rules.tar.gz
> sudo cp community-rules/* /etc/snort/rules/

<img width="1831" height="570" alt="image" src="https://github.com/user-attachments/assets/54309630-caeb-4b96-bebf-270eb811a7ca" />

### Step 5: Test Snort Configuration
##### Command:
> sudo snort -i ens33 -c /etc/snort/snort.conf

<img width="829" height="27" alt="image" src="https://github.com/user-attachments/assets/c367a067-44e1-4fd5-927c-b2dc0fa7e746" />
<img width="776" height="593" alt="image" src="https://github.com/user-attachments/assets/e03bfc6b-a015-43cd-9ede-17492e0b3e40" />

### Step 6: Running Snort in IDS Mode
##### Command:
> sudo snort -c /etc/snort/snort.conf -i ens33

<img width="637" height="186" alt="image" src="https://github.com/user-attachments/assets/54704b20-f090-4ad5-9df5-45ef8ed6d92e" />

### Step 7: Viewing Snort Logs
##### Command:
> checking in the directory - /var/log/snort/

It contains 3 files. File snort.alert.fast is in human readable format while the other 2 are in binary.

<img width="603" height="278" alt="image" src="https://github.com/user-attachments/assets/233c63b6-e1cf-4a94-9b55-3329109a247b" />
<img width="1761" height="236" alt="image" src="https://github.com/user-attachments/assets/12302154-7203-49fe-ba56-bf627f645495" />
<img width="930" height="171" alt="image" src="https://github.com/user-attachments/assets/b6df4a15-c64d-45d7-b3f2-ee29fc1c9fd6" />

### Step 8: Running Snort as a Daemon
##### Command:
> sudo snort -D -c /etc/snort/snort.conf -i ens33

<img width="1329" height="324" alt="image" src="https://github.com/user-attachments/assets/3e3309df-cdd8-4b2f-8f7e-37900c45ebf5" />
<img width="795" height="378" alt="image" src="https://github.com/user-attachments/assets/8255209d-0499-4795-8dac-266024964b69" />
Using command top:
<img width="1064" height="319" alt="image" src="https://github.com/user-attachments/assets/23824c38-d0e1-4242-a900-9c94b512b28a" />

To stop the snort process from running we can find its PID and kill the process (sudo kill PID) or kill all the snort processes (sudo pkill snort)

