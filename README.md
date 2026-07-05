# 🛡️ Detection Engineering Home Lab

> A self-built SIEM lab demonstrating the complete detection engineering lifecycle—from deploying a SIEM and ingesting logs to authoring custom detection rules, simulating real-world attacks, and validating detections mapped to the MITRE ATT&CK framework.

Built entirely in **VirtualBox** to develop and showcase practical SOC Analyst and Detection Engineering skills.

---

## 📌 Project Status

**Current Status:** ✅ Core detection pipeline fully implemented and validated end-to-end.

The lab successfully demonstrates:

* SIEM deployment
* Log ingestion and normalization
* Detection rule development
* Attack simulation
* Alert generation and investigation

The environment is actively expanding with additional detection content, Windows telemetry, and alert tuning.

---

# 🎯 Objectives

This project was built to gain hands-on experience with the technologies and workflows used by Security Operations Centers (SOC).

Key goals include:

* Deploy a production-style SIEM environment
* Build reliable log ingestion pipelines
* Normalize telemetry using Elastic Common Schema (ECS)
* Develop custom detection rules
* Simulate realistic attacks safely
* Validate detections through end-to-end testing
* Map detections to the MITRE ATT&CK framework
* Continuously improve detection quality through tuning

---

# 🛠️ Technology Stack

| Component          | Purpose                                          |
| ------------------ | ------------------------------------------------ |
| Elasticsearch 8.13 | Log storage and search engine                    |
| Kibana 8.13        | SIEM interface, dashboards, detections, alerting |
| Filebeat 8.13      | Log shipping and ECS normalization               |
| Ubuntu 25.04       | SIEM and target system                           |
| Kali Linux         | Attacker machine                                 |
| Hydra              | SSH brute-force simulation                       |
| VirtualBox         | Virtualization platform                          |
| MITRE ATT&CK       | Detection mapping                                |

---

# 🧱 Lab Architecture

```text
+-------------------------+         Host-Only Network          +-------------------------+
|     Kali Attacker       |        192.168.56.0/24             |      Ubuntu SIEM        |
|     192.168.56.102      | ---------------------------------> |     192.168.56.101      |
|                         |     SSH Brute Force (Hydra)        |                         |
| - Hydra                 |                                    | - Elasticsearch         |
| - Attack Tooling        |                                    | - Kibana               |
|                         |                                    | - Filebeat             |
|                         |                                    | - OpenSSH Target       |
+-------------------------+                                    +-------------------------+
```

### Detection Pipeline

```text
SSH Authentication Failure
        │
        ▼
/var/log/auth.log
        │
        ▼
Filebeat (System Module)
        │
        ▼
Elasticsearch (ECS)
        │
        ▼
Custom KQL Detection Rule
        │
        ▼
Kibana Alert
```

---

# 🔍 What This Lab Demonstrates

* ✅ Elastic Stack deployment from scratch
* ✅ Log ingestion with Filebeat
* ✅ ECS log normalization
* ✅ Custom KQL detection engineering
* ✅ MITRE ATT&CK mapping
* ✅ SSH brute-force simulation
* ✅ Alert generation in Kibana
* ✅ Source IP attribution
* ✅ End-to-end validation of the detection pipeline

---

# ⚔️ Attack Scenario

## MITRE ATT&CK

**Technique:** T1110 – Brute Force

### Objective

Simulate an SSH brute-force attack against the Ubuntu target and verify that the SIEM correctly detects malicious authentication activity.

---

## Detection Rule

**Rule Name**

```
Failed Authentication Attempts Detected
```

| Setting         | Value                                                         |
| --------------- | ------------------------------------------------------------- |
| Query Language  | KQL                                                           |
| Query           | `event.category:"authentication" AND event.outcome:"failure"` |
| Severity        | Medium                                                        |
| Risk Score      | 47                                                            |
| Index           | `filebeat-*`                                                  |
| MITRE Technique | T1110 – Brute Force                                           |

---

## Attack Simulation

Executed from the Kali attacker VM:

```bash
hydra -l ibrahim -P passwords.txt -t 4 ssh://192.168.56.101
```

---

## Detection Results

The detection successfully generated alerts in Kibana.

Validated outcomes include:

* Detection triggered successfully
* Target host correctly identified
* Source IP captured
* ECS fields populated correctly
* MITRE ATT&CK mapping applied
* End-to-end detection pipeline validated

---

# 📸 Screenshots

> *(Replace the placeholders below with screenshots stored in `/images`.)*

| Screenshot                   | Description                          |
| ---------------------------- | ------------------------------------ |
| `images/kibana-alerts.png`   | Detection alerts in Kibana           |
| `images/detection-rule.png`  | Custom KQL detection rule            |
| `images/hydra-attack.png`    | Hydra brute-force attack             |
| `images/discover-events.png` | ECS-normalized authentication events |
| `images/filebeat-status.png` | Filebeat service running             |

---

# 📚 Key Lessons Learned

One of the biggest takeaways from this project was that **successful detections depend far more on data quality than on detection logic alone.**

Most engineering effort went into ensuring that Filebeat correctly parsed authentication logs into the appropriate ECS fields:

* `event.category`
* `event.outcome`
* `host.name`
* `source.ip`

Without proper normalization, even a perfectly written detection rule produces no meaningful alerts.

Another important lesson was that **a detection firing is only the beginning of the engineering process.** Effective detections should also be measured by:

* Detection latency
* Alert quality
* False positive rate
* Alert volume
* Analyst usability

---

# 🗺️ Roadmap

### Windows Detection

* [ ] Windows 10 endpoint
* [ ] Sysmon (SwiftOnSecurity configuration)
* [ ] Winlogbeat integration
* [ ] Detect Event IDs 4624, 4625, 4688

### Detection Engineering

* [ ] Additional MITRE ATT&CK techniques
* [ ] Network scanning detections
* [ ] Account creation detections
* [ ] Credential access detections
* [ ] Persistence detections

### SIEM Improvements

* [ ] MITRE ATT&CK coverage dashboard
* [ ] Detection tuning
* [ ] Alert enrichment
* [ ] Dashboard improvements

---

# 🚀 Future Improvements

Following feedback from experienced detection engineers, planned enhancements include:

* Alert suppression and grouping
* Incident-level correlation
* Grouping by target host rather than source IP
* Preserve `first_seen`, `last_seen`, and event count
* Measure Mean Time To Detect (MTTD)
* Improve detection fidelity while reducing analyst fatigue

---

# ⚙️ Lab Configuration

## Network

| Machine       | IP Address     |
| ------------- | -------------- |
| Ubuntu SIEM   | 192.168.56.101 |
| Kali Attacker | 192.168.56.102 |

Both virtual machines use:

* **Adapter 1:** NAT (Internet connectivity)
* **Adapter 2:** Host-only Network (192.168.56.0/24)

---

# 💡 Filebeat Notes

One issue encountered during deployment involved the Filebeat System module.

The `system.yml` module requires both the `syslog` and `auth` filesets to be explicitly enabled:

```yaml
syslog:
  enabled: true

auth:
  enabled: true
```

Without enabling these filesets, Filebeat exits with:

> `module system is configured but has no enabled filesets`

The `auth` fileset is responsible for parsing `/var/log/auth.log`, which contains SSH authentication events.

---

# 🔐 Security Notice

All credentials shown throughout this repository are placeholders.

No production passwords, API keys, secrets, or sensitive information are committed to version control.

This follows secure software development and security engineering best practices.

---

# 👨‍💻 About This Project

This home lab was built as a hands-on portfolio project to demonstrate practical Detection Engineering and SOC Analyst skills.

The project emphasizes:

* SIEM deployment
* Detection engineering
* Log pipeline engineering
* ECS normalization
* Threat simulation
* Incident detection
* MITRE ATT&CK mapping
* Continuous detection improvement

The repository will continue to grow with additional attack simulations, detection rules, Windows telemetry, Sigma rule development, and broader SOC use cases.


