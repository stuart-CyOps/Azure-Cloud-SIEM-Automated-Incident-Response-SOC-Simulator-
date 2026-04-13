# Azure-Cloud-SIEM-Automated-Incident-Response-SOC-Simulator

# Cloud SIEM & Automated Incident Response (SOC Simulator)

## Objective
To architect a cloud-based SOC environment to detect and automatically remediate brute-force attacks.

## Phase 1: Ingestion & Exposure (Setting the Honeypot)
I deployed a Windows VM and connected it to Microsoft Sentinel via the **Azure Monitor Agent (AMA)**.

![Data Ingestion](images/AMA%20connector%20with%20a%20green%20%22Connected%22%20status.png)
*Caption: Confirming the Windows Security Events connector is active and streaming logs.*

To ensure the SIEM captured all attack attempts, I logged into the Windows VM and disabled all local firewalls (Domain, Private, and Public).

![Disabling Local Firewall](images/Set%20the%20state%20to%20Off%20for%20Domain%2C%20Private%2C%20and%20Public%20profiles.png)
*Caption: Disabling the Windows Defender Firewall to allow traffic to reach the OS for log generation.*

Finally, I configured the Network Security Group (NSG) with a rule titled `DANGER-Allow-Any-Any` to expose Port 3389.

![Honeypot Exposure](images/Inbound%20Security%20Rule.png)
*Caption: Intentionally exposing the VM to the public internet to attract brute-force traffic.*

## Phase 2: Detection & KQL Hunting
I used the **IP Addresses** overview to identify my management IP versus the attacker IPs being logged.

![IP Management](images/IP%20Addresses.png)
*Caption: Managing public/private IP interfaces to distinguish between legitimate administrative traffic and malicious probes.*

I then wrote a KQL query to identify attackers who failed to log in more than 10 times.

![KQL Hunting](images/KQL%20code%20+%20Results%20table.png)
*Caption: KQL query results identifying high-volume brute-force attempts from multiple international IPs.*

## Phase 3: SOAR Automation (The "PB-Block-Attacker-IP" Playbook)
To automate the response, I mapped the `IpAddress` entity and built a Logic App.

![Automation Workflow](images/Logic%20app%20designer%20flow.png)
*Caption: The logic flow: Sentinel Trigger -> Get IP Entities -> Create NSG Deny Rule.*

## Phase 4: Verification of Automated Defense
When the incident triggered, the playbook successfully executed.

![Playbook Execution](images/Run%20Playbook.png)
*Caption: Confirmation that the 'PB-Block-Attacker-IP' playbook was triggered successfully on Incident ID 2.*

The final result is a new, automatically generated security rule that blacklists the specific attacker IP.

![Auto-Block Rule](images/Successful%20Playbook%20Run%20Creates%20New%20inbound%20rule.png)
*Caption: The NSG now contains a 'Deny' rule for the attacker's IP (60.250.231.169), created automatically by the SOAR playbook.*

