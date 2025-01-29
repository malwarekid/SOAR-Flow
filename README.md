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

### **1️⃣ Install Wazuh SIEM**  
Follow the official Wazuh installation guide:  
🔗 [Wazuh Installation Guide](https://documentation.wazuh.com/current/installation-guide/index.html)  

### **2️⃣ Install TheHive**  
Follow the official documentation for installing TheHive:  
🔗 [TheHive Installation Guide](https://docs.strangebee.com/thehive/installation/)  

### **3️⃣ Install Shuffle SOAR**  
Run the following commands to install **Shuffle SOAR** on Ubuntu:  
```bash
# Install Docker if not already installed
sudo apt update && sudo apt install -y docker.io docker-compose

# Clone the Shuffle repository
git clone https://github.com/frikky/Shuffle.git
cd Shuffle

# Run the installation script
sudo ./install.sh
```
# Access Shuffle Web UI at http://<your-ip>:5001

🔗 [Shuffle Installation Guide](https://shuffler.io/docs)  

### **4️⃣ Create a Discord Webhook**  
1. Go to your **Discord Server** → **Settings** → **Integrations** → **Webhooks**  
2. Click **New Webhook** → Name it **SOC Alerts**  
3. Copy the **Webhook URL** (you will need it later)  

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
