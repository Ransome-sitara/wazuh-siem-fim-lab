# Wazuh SIEM & File Integrity Monitoring (FIM) Lab

![Wazuh Version](https://img.shields.io/badge/Wazuh-v4.14.8-blue)
![Platform](https://img.shields.io/badge/Platform-Docker%20%7C%20Linux%20%7C%20Windows-lightgrey)
![Status](https://img.shields.io/badge/Status-Completed-success)

## Project Overview
This repository contains the complete deployment, configuration, and analysis for a Security Operations Center (SOC) lab focused on Endpoint Monitoring and File Integrity Monitoring (FIM). 

The objective was to establish centralized visibility into a Windows 11 endpoint by deploying a Wazuh SIEM environment. The project demonstrates the ability to detect unauthorized file creation, modification, and deletion in real-time, a critical control for identifying ransomware, insider threats, and unauthorized system changes.

## Lab Architecture

| Component | Host | Role |
| :--- | :--- | :--- |
| **Wazuh Manager** | Kali Linux (Docker) | Central SIEM processing engine. Ingests, analyzes, and stores telemetry. |
| **Wazuh Indexer** | Kali Linux (Docker) | OpenSearch backend for storing alerts and logs. |
| **Wazuh Dashboard** | Kali Linux (Docker) | Web interface for SOC analysts to triage alerts. |
| **Wazuh Agent** | Windows 11 (Physical) | Endpoint telemetry collection (FIM, Event Logs). |

## Objectives Achieved
- [x] Deployed Wazuh Manager via Docker Compose on Kali Linux.
- [x] Enrolled a Windows 11 endpoint as a monitored agent.
- [x] Configured File Integrity Monitoring (FIM) with `realtime="yes"` on a targeted directory (`C:\Users\HP\SensitiveFiles`).
- [x] Simulated malicious activity (file creation, modification, deletion).
- [x] Triaged and analyzed alerts (Rules 550, 553, 554) via the Wazuh Dashboard.
- [x] Documented a full professional incident report.

## Evidence & Dashboard Alerts

### Infrastructure Health
The Wazuh containers were successfully deployed and the Indexer cluster reported a healthy "Green" status.
*(See `/evidence/01_Docker_Containers_Running.png`)*

### Endpoint Connectivity
The Windows Agent successfully authenticated and registered with the Manager.
*(See `/evidence/04_Agent_Active_Status.png`)*

### File Integrity Monitoring Alerts
A test file (`secret_plan.txt`) was created, modified, and deleted. The Wazuh Dashboard successfully detected the activity and triggered real-time alerts.
*(See `/evidence/08_FIM_Dashboard_Alerts.png`)*

## Repository Structure
```text
├── README.md                 # Project documentation (this file)
├── lab_report.md             # Full SOC Lab Report (Markdown format)
├── SOC_Lab_Report.pdf        # Professional PDF version of the report
├── ossec.conf                # Sample Agent configuration for Windows FIM
└── evidence/                 # Screenshots proving lab execution
    ├── 01_Docker_Containers_Running.png
    ├── 02_Indexer_Cluster_Green.png
    ├── ...
    └── 11_Test_File_Created.png
```

# Key Technical Challenges Overcome

During the deployment, several technical hurdles were resolved:

   Infrastructure Provisioning: Pivoted from cloud-based infrastructure (due to resource capacity limits) to a local Docker deployment on Kali Linux for higher reliability.

   Docker Resource Tuning: Adjusted Linux kernel parameters (vm.max_map_count=262144) to allow the OpenSearch/Wazuh Indexer to initialize successfully.

   Windows XML Configuration: Troubleshot silent XML parsing errors in the Windows ossec.conf by using PowerShell to force proper encoding and formatting, ensuring the FIM engine successfully mounted the target directory.

# Tools Used

   Wazuh (v4.14.8): SIEM and XDR platform.

   Docker & Docker Compose: Containerization for the Manager stack.

   Kali Linux: Host OS for the SOC environment.

   Windows 11: Target endpoint.

   PowerShell: For agent management and log verification.

# Replication Steps

## To replicate this lab environment:

   Clone the Wazuh Docker repository: git clone https://github.com/wazuh/wazuh-docker.git -b v4.14.8

   Navigate to the single-node directory: cd wazuh-docker/single-node

   Generate certificates: docker compose -f generate-indexer-certs.yml run --rm generator

   Start the stack: docker compose up -d

   Install the Wazuh Agent MSI on a Windows machine and configure it to point to the Kali host IP.

   Edit C:\Program Files (x86)\ossec-agent\ossec.conf to add the FIM directory directive.

   Restart the agent and access the Dashboard at https://localhost.

Disclaimer: This lab was conducted in an isolated environment for educational and professional development purposes.
