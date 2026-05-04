# Project Report - SOC Mini System Architecture.

**Submited by**: Nizamudheen KN  
**Date Created**: 03-05-2026  
**Category**: S0C (Security Operations Center)  
**Platform**: AWS (Amazon Web Services)  

---

## Project Summary.

<img src="images/diagram.png">

This is a real world mini SOC workflow. The main goal is to detect external threats like port scanning, ssh brute force, ping from external IP and checking the external IP's reputation. If IP is malicious then we will be blocking that IP address using IP tables in the endpoint else we will create a ticket in theHive. SO the work flow be like Endpoint which is installed with wazuh (SIEM) agent and suricata IDS sends logs to the wazuh server ➡️ Wazuh server cross checks every events with the rules and if rules matches then triggers alert ➡️ N8N webhook captures the alerts thats being triggered in the wazuh and starts executing the playbook ➡️ If IP is malicious then blocks the IP using IP tables firewall in the endpoint ➡️ Raises ticket in theHive platform.

## Step-1 Setting up the AWS instance.

Since the project requires more system specs i decided to do it in AWS by creating EC2 instances. Since AWS provide free trial to new users with $120 credits it is quite easy to set up.

<img width="1920" height="954" alt="image" src="https://github.com/user-attachments/assets/29db0f85-2425-44c4-934e-fa254fdaaaea" />

Here I have created 4 EC2 instances:
1. **wazuh-server** - is the instances where we are running wazuh SIEM.
1. **soc-linux-endpoint** - is the instance which is coinsidered as an linux endpoint with wazuh agent and suricata IDS installed.
1. **soc-thehive** - is the instance which is running theHive platform.
1. **soc-n8n** - is the instance running n8n server.

Basically these four instances serves 4 purposes, one is working as a SIEM tool, one is working an endpoint like a linux desktop or server, one is working as a ticket raising platform, one is working as a SOAR platform to automate responses. 

## Linux Endpoint
As I mentioned already `soc-linux-endpoint` is the endpoint which will be monitoring for threats and FIM (file integrity monitoring), we already installed and configured the wazuh agent and suricata and wazuh is sending logs to the wazuh server along with suricata alerts. So suricata will be checking all the network traffic going in and through the endpoint and when ever the suricata alert is triggered then that alert will also be sent to wazuh manager via wazuh agent.

<img width="1921" height="1080" alt="image" src="https://github.com/user-attachments/assets/909d3529-5fba-4197-bfdc-7178c31ef5e9" />

## Wazuh Server
So this `wazuh-server` instance where we are running wazuh SIEM will be receiving the logs from the endpoint as well as the suricata alerts. Wazuh servers main job role is to normalize and correlate the logs with the rules inorder to trigger alerts. When ever an alert is triggered in the SIEM the alert will be forwarded to the N8N webhook.

<img width="1919" height="955" alt="image" src="https://github.com/user-attachments/assets/5479a745-0349-4d59-a258-87672ff41d87" />

## N8N Server
So this so called `soc-n8n` is the instance where we hosted n8n server. So every elert triggered in wazuh SIEM will be forwarded to n8n to automate stuffs like active response and reputation checks.

<img width="1919" height="955" alt="image" src="https://github.com/user-attachments/assets/4ae7412c-543f-42b1-bfb0-02464fec7a05" />

 ## The Hive
`soc-theHive` is the endpoint where we running theHive server which is our ticket raising tool. So theHive will be our ticket raising tool. And the n8n automates the process of creating ticket based on the incomming wazuh alerts.

<img width="1919" height="955" alt="image" src="https://github.com/user-attachments/assets/26707fdd-dcb5-4be7-bd1f-956f5a73fd6b" />

