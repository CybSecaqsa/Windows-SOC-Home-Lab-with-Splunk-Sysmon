# Windows-SOC-Home-Lab-with-Splunk-Sysmon

## Objective

Build a controlled SOC environment to collect, monitor, detect, and investigate Windows endpoint telemetry using Sysmon and Splunk. The project demonstrates a basic SOC workflow from endpoint telemetry collection through SIEM analysis, alerting, investigation, and documentation.

## Skills learned


- Security monitoring
- SIEM operations
- SPL searching and analysis
- Alert investigation
- Basic detection engineering
- Sysmon endpoint telemetry
- Windows process, file, network, and DNS monitoring
- Incident documentation
- False-positive analysis
- MITRE ATT&CK mapping
- Virtual lab administration

## Tools used

- Windows 11
- Sysmon v15.21
- Splunk Universal Forwarder
- Splunk Enterprise
- Ubuntu Server 24.04.4 LTS
- PowerShell
- VirtualBox
- MITRE ATT&CK

## Steps

1. Build the Virtual Lab

Set up a Windows 11 endpoint and an Ubuntu Server VM using VirtualBox. Configure NAT and Host-Only networking so the systems can communicate inside the lab.

2. Configure Sysmon

Install Sysmon and configure telemetry collection for:

- Process Creation — Event ID 1
- Network Connection — Event ID 3
- File Creation — Event ID 11
- Registry Events — Event IDs 12–14
- DNS Query — Event ID 22

 3. Configure Splunk Enterprise

Install Splunk Enterprise on Ubuntu Server, create the soc_lab index, and configure the receiving port 9997.

4. Configure Splunk Universal Forwarder

Configure the Windows Universal Forwarder to collect:
Microsoft-Windows-Sysmon/Operational

Use:

[WinEventLog://Microsoft-Windows-Sysmon/Operational]
- disabled = 0
- index = soc_lab
- sourcetype = WinEventLog:Sysmon
- renderXml = 0

5. Verify Ingestion

Use:

index=soc_lab sourcetype=WinEventLog:Sysmon

Verify that Windows Sysmon events are visible in Splunk

6. Create Detection Rules

Example file-creation detection:

index=soc_lab sourcetype=WinEventLog:Sysmon EventCode=11

Example Command Prompt detection:

index=soc_lab sourcetype=WinEventLog:Sysmon EventCode=1
| search Image="*\cmd.exe"

7. Test the Detections

Use harmless test activity.

File creation:

New-Item -Path "$env:TEMP\SOC-Lab-Test.txt" -ItemType File -Force

Command Prompt:

cmd.exe /c echo SOC-Lab-CMD-Test

Confirm that Sysmon records the activity and Splunk triggers the corresponding alert.

8. Investigate Alerts

Review the event details, process information, user context, command line, and related activity. Determine whether the activity is benign, suspicious, or malicious. Assign severity and document the investigation.

9. Map Relevant Activity

Map investigated behavior to MITRE ATT&CK where appropriate. For example, the controlled Command Prompt test was mapped to T1059.003 — Windows Command Shell.

10. Build the SOC Dashboard

Create a dashboard showing:

- Total Sysmon Events
- Events by Event Type
- Recent Process Creation
- Recent File Creation
- Recent Network Connections
- Recent DNS Queries

Example Investigation

SOC-001 — Command Prompt Activity

Event: Sysmon Event ID 1

- Verdict: Benign / Authorized Test Activity
- Severity: Low
- MITRE ATT&CK: T1059.003 — Windows Command Shell
- Containment: Not required

A controlled cmd.exe test generated a Sysmon process-creation event and triggered the Splunk detection. The activity was identified as authorized lab testing rather than malicious behavior.

            
## Screenshots

01. SOC-dashboard
   
<img width="940" height="381" alt="Screenshot 2026-09-07 213831" src="https://github.com/user-attachments/assets/6816c5e7-f4ec-43f5-9b07-a711bb2bc75c" />

The completed Windows SOC Overview dashboard   

02. Alert-investigation

<img width="863" height="415" alt="Screenshot 2026-09-07 183052" src="https://github.com/user-attachments/assets/092fcc81-e6cd-43ad-b1a0-799e05d160e2" />

Investigation of SOC-001 — Command Prompt Activity

03. Incident Report

<img width="253" height="350" alt="Screenshot 2026-09-07 185513" src="https://github.com/user-attachments/assets/342efed1-0422-4ce9-a676-b877536c58f6" />

Documented investigation/incident report   

04. Network Investigation

<img width="740" height="273" alt="Screenshot 2026-09-07 211059" src="https://github.com/user-attachments/assets/c1112589-0a60-4f49-b9b3-81cad102cfb0" />
<img width="887" height="378" alt="Screenshot 2026-09-07 211531" src="https://github.com/user-attachments/assets/dc20f0c9-2720-4025-948e-b4a404eb5889" />

SOC-002 Microsoft Defender network investigation   
