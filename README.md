---

# 🚀 SOAR-Flow  

## 📌 Project Description  
This project demonstrates how to integrate **Shuffle SOAR** with **Wazuh SIEM** and **TheHive** to automate incident response.

✅ **Receiving security alerts** from Wazuh.  
✅ **Enriching alerts** using external threat intelligence (VirusTotal, AbuseIPDB).  
✅ **Creating an incident** in TheHive for case management.  
✅ **Sending notifications** to a Discord channel.  
✅ **(Bonus)** Auto-mitigating threats (e.g., blocking malicious IPs).  

By implementing this SOAR workflow, you can **automate security operations**, reduce response time, and improve efficiency in a **SOC environment**.  

---

## 🔧 Tools Used  
| Tool            | Description |
|----------------|------------|
| **Wazuh SIEM** | Security Information & Event Management (SIEM) solution for threat detection. |
| **TheHive**    | Open-source Security Incident Response Platform (SIRP). |
| **Shuffle**    | Open-source Security Orchestration, Automation, and Response (SOAR) platform. |
| **VirusTotal API** | Used for malware and URL reputation checks. |
| **AbuseIPDB API** | Used for checking if an IP address is malicious. |
| **Discord Webhook** | Sends alerts to a Discord channel for real-time monitoring. |

---

## 🛠️ Installation & Setup  

### VM-1 for Wazuh and TheHive 

**Specifications**

- **RAM:** 12GB+
- **HDD:** 60GB+
- **OS:** Ubuntu 24.04 LTS

### **1️⃣ Install Wazuh SIEM**  
Follow the official Wazuh installation guide:  
🔗 [Wazuh Installation Guide](https://documentation.wazuh.com/current/installation-guide/index.html)  

1. **Update and Upgrade:**
   ```bash
   apt-get update && apt-get upgrade
   ```

2. **Install Wazuh 4.10:**
   ```bash
   curl -sO https://packages.wazuh.com/4.10/wazuh-install.sh && sudo bash ./wazuh-install.sh -a
   ```

3. **Extract Wazuh Credentials:**
   ```bash
   sudo tar -xvf wazuh-install-files.tar
   ```

4. **Wazuh Dashboard Credentials:**
   - **User:** admin
   - **Password:** ***************

5. **Access Wazuh Dashboard:**
   - Open your browser and go to: `https://<Public IP of Wazuh>`

### **2️⃣ Install TheHive**  
Follow the official documentation for installing TheHive:  
🔗 [TheHive Installation Guide](https://docs.strangebee.com/thehive/installation/)  

1. **Install Dependencies:**
   ```bash
   apt install wget gnupg apt-transport-https git ca-certificates ca-certificates-java curl  software-properties-common python3-pip lsb-release
   ```

2. **Install Java:**
   ```bash
   wget -qO- https://apt.corretto.aws/corretto.key | sudo gpg --dearmor  -o /usr/share/keyrings/corretto.gpg
   echo "deb [signed-by=/usr/share/keyrings/corretto.gpg] https://apt.corretto.aws stable main" |  sudo tee -a /etc/apt/sources.list.d/corretto.sources.list
   sudo apt update
   sudo apt install java-common java-11-amazon-corretto-jdk
   echo JAVA_HOME="/usr/lib/jvm/java-11-amazon-corretto" | sudo tee -a /etc/environment 
   export JAVA_HOME="/usr/lib/jvm/java-11-amazon-corretto"
   ```

3. **Install Cassandra:**
   ```bash
   wget -qO -  https://downloads.apache.org/cassandra/KEYS | sudo gpg --dearmor  -o /usr/share/keyrings/cassandra-archive.gpg
   echo "deb [signed-by=/usr/share/keyrings/cassandra-archive.gpg] https://debian.cassandra.apache.org 40x main" |  sudo tee -a /etc/apt/sources.list.d/cassandra.sources.list
   sudo apt update
   sudo apt install cassandra
   ```

4. **Install ElasticSearch:**
   ```bash
   wget -qO - https://artifacts.elastic.co/GPG-KEY-elasticsearch |  sudo gpg --dearmor -o /usr/share/keyrings/elasticsearch-keyring.gpg
   sudo apt-get install apt-transport-https
   echo "deb [signed-by=/usr/share/keyrings/elasticsearch-keyring.gpg] https://artifacts.elastic.co/packages/7.x/apt stable main" |  sudo tee /etc/apt/sources.list.d/elastic-7.x.list
   sudo apt update
   sudo apt install elasticsearch
   ```

5. **Install TheHive:**
   ```bash
   wget -O- https://archives.strangebee.com/keys/strangebee.gpg | sudo gpg --dearmor -o /usr/share/keyrings/strangebee-archive-keyring.gpg
   echo 'deb [signed-by=/usr/share/keyrings/strangebee-archive-keyring.gpg] https://deb.strangebee.com thehive-5.2 main' | sudo tee -a /etc/apt/sources.list.d/strangebee.list
   sudo apt-get update
   sudo apt-get install -y thehive
   ```

6. **Default Credentials for TheHive:**
   - **Port:** 9000
   - **Credentials:** 'admin@thehive.local' with a password of 'secret'

### VM-2 for Shuffle  

**Specifications**

- **RAM:** 4GB+
- **HDD:** 40GB+
- **OS:** Ubuntu 24.04 LTS

### **3️⃣ Install Shuffle SOAR**  
Run the following commands to install **Shuffle SOAR** on Ubuntu:  
🔗 [Shuffle Installation Guide](https://shuffler.io/docs)  

```bash
# Install Docker if not already installed
sudo apt update && sudo apt install -y docker.io docker-compose

# Enable and start Docker
sudo systemctl enable docker
sudo systemctl start docker

# Clone the Shuffle repository
git clone https://github.com/Shuffle/Shuffle.git
cd Shuffle

# Build and run Shuffle with Docker Compose
sudo docker-compose up -d
```
Access Shuffle Web UI at http://YOUR-IP:3001

### **4️⃣ Create a Discord Webhook**  
1. Go to your **Discord Server** → **Settings** → **Integrations** → **Webhooks**  
2. Click **New Webhook** → Name it **SOC Alerts**  
3. Copy the **Webhook URL** (you will need it later)  

---

## Configuration for TheHive

### Configure Cassandra

1. **Edit Cassandra Config File:**
   ```bash
   nano /etc/cassandra/cassandra.yaml
   ```

2. **Change Cluster Name:**
   ```yaml
   cluster_name: 'SOAR-Flow'
   ```

3. **Update Listen Address:**
   ```yaml
   listen_address: <public IP of TheHive>
   ```

4. **Update RPC Address:**
   ```yaml
   rpc_address: <public IP of TheHive>
   ```

5. **Update Seed Provider:**
   ```yaml
   - seeds: "<Public IP Of the TheHive>:7000"
   ```

6. **Stop Cassandra Service:**
   ```bash
   systemctl stop cassandra.service
   ```

7. **Remove Old Files:**
   ```bash
   rm -rf /var/lib/cassandra/*
   ```

8. **Restart Cassandra Service:**
   ```bash
   systemctl start cassandra.service
   ```

### Configure ElasticSearch

1. **Edit ElasticSearch Config File:**
   ```bash
   nano /etc/elasticsearch/elasticsearch.yml
   ```

2. **Update Cluster Name and Host:**
   ```yaml
   cluster.name: thehive
   node.name: node-1
   network.host: <Public IP of your TheHive instance>
   http.port: 9200
   discovery.seed_hosts: ["127.0.0.1"]
   cluster.initial_master_nodes: ["node-1"]
   ```

3. **Start ElasticSearch Service:**
   ```bash
   systemctl start elasticsearch
   systemctl enable elasticsearch
   systemctl status elasticsearch
   ```

## Configure TheHive

1. **Ensure Proper Ownership:**
   ```bash
   ls -la /opt/thp
   chown -R thehive:thehive /opt/thp
   ```

2. **Edit TheHive Configuration File:**
   ```bash
   nano /etc/thehive/application.conf
   ```

3. **Update Database and Index Configuration:**
   ```conf
   db.janusgraph {
     storage {
       backend = cql
       hostname = ["<Public IP of TheHive>"]
       cql {
         cluster-name = SOAR-Flow
         keyspace = thehive
       }
     }
   }

   index.search {
     backend = elasticsearch
     hostname = ["<Public IP of TheHive>"]
     index-name = thehive
   }

   application.baseUrl = "http://<Public IP of TheHive>:9000"
   ```

4. **Start TheHive Services:**
   ```bash
   systemctl start thehive
   systemctl enable thehive
   systemctl status thehive
   ```

---

## 🔄 Workflow - Automating Incident Response  

### **📌 Workflow Overview**  
This workflow automates incident response using Shuffle:  
1️⃣ **Receive alerts** from Wazuh SIEM when suspicious activity is detected.  
2️⃣ **Enrich the alert** using VirusTotal & AbuseIPDB API.  
3️⃣ **Create an incident** in TheHive for case tracking.  
4️⃣ **Send a notification** to Discord with alert details.  
5️⃣ **(Optional)** Perform auto-mitigation (e.g., blocking malicious IPs).  

### **📌 Shuffle Workflow Steps**  

🔹 **Step 1: Add Wazuh Alert as Trigger**  
- In Shuffle, create a **new workflow** and add a **Webhook trigger**.  
- Configure Wazuh to send alerts via **webhooks**.  

🔹 **Step 2: Enrich Data with VirusTotal & AbuseIPDB**  
- Add an **HTTP Request** node to check IPs/Hashes using VirusTotal API.  
- Add another HTTP Request to query AbuseIPDB for malicious IPs.  

🔹 **Step 3: Create an Incident in TheHive**  
- Use TheHive API to create a **new case** with alert details.  

🔹 **Step 4: Send Alert to Discord**  
- Use the Discord Webhook to send a formatted message to a SOC channel.  

🔹 **Step 5: (Optional) Auto-Mitigation**  
- If the IP is **high risk**, trigger a firewall rule to **block the attacker**.  

---

## 🚀 Running the Workflow  

### **Step 1: Configure Wazuh to Send Alerts to Shuffle**  
Edit the Wazuh **ossec.conf** file to send webhook alerts:  
```xml
<integration>
  <name>custom-webhook</name>
  <hook_url>http://<shuffle-ip>:5001/webhook</hook_url>
  <event_format>json</event_format>
</integration>
```
Restart Wazuh to apply changes:  
```bash
sudo systemctl restart wazuh-manager
```

### **Step 2: Configure TheHive API Key**  
Generate an API key in TheHive and add it to Shuffle’s HTTP Request node.  

### **Step 3: Configure Discord Webhook in Shuffle**  
Use the **Discord Webhook URL** in the Shuffle **HTTP Request** node.  
Example payload:  
```json
{
  "content": "**🚨 New Security Alert 🚨**\n\nIP: 192.168.1.100\nSeverity: High\nSource: Wazuh SIEM"
}
```

### **Step 4: Test the Workflow**  
- Trigger an alert in Wazuh (e.g., failed SSH logins).  
- Verify the incident is created in TheHive.  
- Check if the alert is sent to Discord.  

---

## 📌 Example Output  

✅ **TheHive Incident Created:**  
```
[INFO] New Incident Created in TheHive:
- Title: Suspicious SSH Login Attempts
- Severity: High
- Source: Wazuh SIEM
```

✅ **Discord Alert:**  
![Discord Alert Example](https://i.imgur.com/example.png)  

---

## 🎯 Future Enhancements  
🔹 Add auto-mitigation (e.g., blocking attacker IPs via firewall rules).  
🔹 Integrate more threat intelligence feeds (e.g., MISP, Shodan API).  
🔹 Expand automation to handle different types of incidents.  

---

## 📜 License  
This project is licensed under the **MIT License**.  

---

## 📬 Contact  
👤 **Author:** [Nitin Sharma](https://github.com/malwarekid)  
💻 **GitHub:** [github.com/malwarekid](https://github.com/malwarekid)  
📧 **Email:** malwarekid@protonmail.com  

---
