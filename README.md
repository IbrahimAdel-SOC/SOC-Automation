# SOC Automation Lab
## Objective

Build a fully automated SOC environment that integrates a SIEM/EDR solution (Wazuh) with a SOAR platform (Shuffle) and a Case Management system (TheHive) to detect, enrich, and respond to security incidents with minimal manual intervention.

---
## Skills Learned

- Deploying and configuring Wazuh Manager as a centralized SIEM/EDR platform
- Installing and managing the Wazuh Agent on Windows 10 endpoints
- Building automated response workflows using Shuffle (SOAR)
- Creating and managing security alerts and cases in TheHive
- Integrating SIEM, SOAR, and Case Management tools in a unified SOC pipeline
- Understanding and implementing Indicator Enrichment for threat intelligence
- Designing and applying Responsive Actions based on detection rules
- Configuring email notifications to alert SOC analysts automatically
- Reading and analyzing telemetry data forwarded from endpoints
- Implementing log archiving for unmatched events

---
## Tools Used

| Tool | Purpose |
|------|---------|
| Wazuh Manager | SIEM / EDR — collects and evaluates telemetry from agents |
| Wazuh Agent | Installed on Windows 10 — sends telemetry to Wazuh Manager |
| Shuffle | SOAR platform — automates the incident response workflow |
| TheHive | Case Management — receives alerts and tracks investigations |
| Windows 10 Client | Endpoint monitored by the Wazuh Agent |

---
## Steps

**Ref 1 — Network Diagram**

Overview of the lab topology showing all components, their assigned public IPs, and how the Windows 10 client (with Wazuh Agent) communicates with the Wazuh Manager over the internet through a router. Shuffle and TheHive are connected to the Wazuh Manager to form the automated SOC pipeline.

<img width="1019" height="820" alt="SOC Automation Diagram drawio" src="https://github.com/user-attachments/assets/8ebf5547-30b5-4dba-af17-5198cb447ef4" />

---

**Ref 2 — TheHive Stack Services Status**

All three backend services required by TheHive are installed and running successfully on the TheHive VM:
- **Cassandra** — active (running) — distributed storage backend for TheHive data
- **Elasticsearch** — active (running) — search and indexing engine used by TheHive
- **TheHive** — active (running) — the main Security Incident Response platform

All services were verified using `systemctl status cassandra elasticsearch thehive`.

<img width="1919" height="937" alt="image" src="https://github.com/user-attachments/assets/30aeeaa4-a03e-4908-9765-9ffbfe3953bd" />

---

**Ref 3 — Sysmon Installation on Windows 10**

Sysmon64 (System Monitor v15.20) successfully installed on the Windows 10 VM.
The PowerShell output confirms: Sysmon64 installed, SysmonDrv installed, and Sysmon64 started.
The Event Viewer shows Sysmon Operational log already capturing **251 events (1.07 MB)**,
confirming that Sysmon is actively monitoring and logging system activity in real time.

<img width="1014" height="791" alt="image" src="https://github.com/user-attachments/assets/0f2ca857-d9da-4e4e-a498-f3001440499e" />

---

**Ref 4 — Cassandra Configuration (cassandra.yaml)**

Key settings configured in `/etc/cassandra/cassandra.yaml`:
- **cluster_name** set to `ibrahim` to identify the Cassandra cluster
- **listen_address** set to `192.168.1.13` for inter-node communication
- **rpc_address** set to `192.168.1.13` to accept client connections from TheHive
- **seeds** set to `192.168.1.13:7000` as the contact point for the cluster

<img width="833" height="535" alt="image" src="https://github.com/user-attachments/assets/d42aa8d9-5b6c-4c6f-8702-343815063d51" />

---

**Ref 5 — Elasticsearch Configuration (elasticsearch.yml)**

Key settings configured in `/etc/elasticsearch/elasticsearch.yml`:
- **cluster.name** set to `ibrahim` to match the Cassandra cluster name
- **node.name** set to `node-1` to identify this Elasticsearch node
- **network.host** set to `192.168.1.13` to accept connections from TheHive
- **http.port** set to `9200` for the REST API
- **cluster.initial_master_nodes** set to `["node-1"]` to bootstrap the cluster

<img width="833" height="269" alt="image" src="https://github.com/user-attachments/assets/731cadae-19e7-4b6b-91ad-532b3d926677" />

---

**Ref 6 — TheHive Configuration (application.conf)**

Key settings configured in `/etc/thehive/application.conf`:
- **storage backend** set to `cql` (Cassandra) with **hostname** `192.168.1.13`
- **cluster-name** set to `ibrahim` to match the Cassandra cluster
- **keyspace** set to `thehive`
- **index backend** set to `elasticsearch` with **hostname** `192.168.1.13`
- **index-name** set to `thehive`
- **application.baseUrl** set to `http://192.168.1.13:9000` as the TheHive web interface URL

<img width="833" height="269" alt="image" src="https://github.com/user-attachments/assets/346b75b6-4e7c-4afc-8ff4-91b85dd3bb1e" />

---

