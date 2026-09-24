# IoT Attack Simulation & Suricata Threat Detection

## Overview

This project demonstrates the detection of simulated IoT security threats using **MQTT, Mosquitto, Suricata IDS, Docker, and a Raspberry Pi**.

The environment was designed to simulate legitimate IoT communications as well as controlled malicious activity against an MQTT broker. Custom Suricata detection rules were created to identify MQTT flooding and malformed payload attacks.

The project demonstrates practical skills in:

- IoT security
- Network intrusion detection
- MQTT security
- Suricata IDS
- Custom IDS rule development
- Network traffic analysis
- PCAP analysis
- Docker containerization
- Raspberry Pi
- Python security scripting

> **Note:** All attack simulations in this project were performed in an isolated educational lab environment for defensive security testing.

---

## Project Architecture

The lab uses a Raspberry Pi as an IoT client and attack-simulation device communicating with a Mosquitto MQTT broker hosted in an AWS lab environment.

```text
                Raspberry Pi
                     |
                     | MQTT Traffic
                     | TCP/1883
                     v
              +--------------+
              |  Mosquitto   |
              | MQTT Broker  |
              +--------------+
                     |
                     | Traffic monitored
                     v
              +--------------+
              |   Suricata   |
              |     IDS      |
              +--------------+
                     |
                     | eve.json alerts
                     v
              +--------------+
              |   Filebeat   |
              +--------------+
                     |
                     v
              Elasticsearch
```

Docker Compose is used to manage the Mosquitto, Suricata, and Filebeat components.

---

## Technologies Used

| Technology | Purpose |
|---|---|
| Raspberry Pi | IoT client and controlled attack simulator |
| Mosquitto | MQTT message broker |
| Suricata | Network intrusion detection |
| Docker | Containerized security services |
| Docker Compose | Container orchestration |
| Filebeat | Suricata log forwarding |
| Elasticsearch | Security event storage |
| Python | MQTT attack simulation scripts |
| Paho MQTT | MQTT client library |
| Scapy | Packet generation |
| tcpdump | Packet capture |
| jq | JSON log analysis |
| Git/GitHub | Version control and project documentation |

---

## Attack Scenario 1 — MQTT Flood Simulation

The first simulation demonstrates an MQTT message-flooding scenario.

A Python script running on the Raspberry Pi publishes MQTT messages at a rate exceeding the configured detection threshold.

The custom Suricata rule monitors traffic to TCP port **1883** and generates an alert when more than **100 packets from a source are observed within 60 seconds**.

### Suricata Rule

**SID:** `2000001`

**Detection purpose:**

```text
MQTT Excessive Publish Rate — Possible Flood Attack
```

The simulation sends 150 MQTT messages in under 60 seconds to trigger the detection rule.

The traffic is captured using `tcpdump` and can also be replayed through Suricata for offline validation.

---

## Attack Scenario 2 — Malformed MQTT Payloads

The second simulation tests Suricata's ability to detect suspicious or malformed MQTT payloads.

The Raspberry Pi sends multiple payload patterns including:

- `undefined`
- JSON-like data containing an undefined value
- Repeated `A` characters representing a fuzzing or buffer-overflow probe
- Raw crafted packets generated with Scapy

Two custom Suricata signatures are used for detection.

### SID 2000002

Detects MQTT payloads containing:

```text
undefined
```

Alert:

```text
MQTT Payload Contains Undefined — Malformed JSON Indicator
```

### SID 2000003

Detects long repeated-byte patterns such as:

```text
AAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAA
```

Alert:

```text
MQTT Oversized Repeated-Byte Payload — Buffer Overflow Probe
```

---

## Detection Workflow

The project follows the following detection process:

```text
Raspberry Pi
     |
     | Generate MQTT test traffic
     v
Mosquitto MQTT Broker
     |
     | Network traffic
     v
Suricata IDS
     |
     | Custom detection rules
     v
eve.json
     |
     | Alert events
     v
Filebeat
     |
     v
Elasticsearch
```

Suricata alerts are recorded in `eve.json` and can be queried using `jq`.

---

## PCAP Validation

Each attack simulation was captured using `tcpdump`.

The captures allow the attacks to be replayed through Suricata to confirm that the detection rules work independently of the live traffic.

### MQTT Flood Capture

```text
mqtt_attack_flood.pcap
```

Used to validate **SID 2000001**.

### Malformed Payload Capture

```text
mqtt_attack_malformed.pcap
```

Used to validate **SID 2000002** and **SID 2000003**.

PCAP files containing lab network information are kept locally rather than published in this repository.

---

## Custom Suricata Rules

The custom detection rules are located in:

```text
suricata/rules/
```

The project includes:

```text
mqtt-rate-limit.rules
mqtt-malformed-json.rules
```

These rules detect:

| SID | Detection |
|---|---|
| 2000001 | Excessive MQTT publish rate / flood |
| 2000002 | Malformed MQTT payload containing `undefined` |
| 2000003 | Repeated-byte / oversized payload probe |

---

## Repository Structure

```text
.
├── filebeat/
├── iot-lab5/
│   ├── mqtt_flood.py
│   ├── mqtt_malformed.py
│   └── mqtt_publish_tls.py
│
├── mosquitto/
│   └── config/
│
├── suricata/
│   └── rules/
│       ├── mqtt-rate-limit.rules
│       └── mqtt-malformed-json.rules
│
├── docker-compose.yml
├── filebeat.yml
├── project2_alerts.json
├── project2_answers.md
└── README.md
```

---

## Detection Evidence

Suricata alerts generated during the simulations are collected in:

```text
project2_alerts.json
```

The evidence includes alerts for:

```text
SID 2000001
SID 2000002
SID 2000003
```

This provides validation that the custom Suricata rules successfully detected the simulated MQTT threats.

---

## Security Considerations

This repository is intended for **educational and defensive cybersecurity purposes**.

Attack simulations were performed only against systems within a controlled lab environment.

Sensitive material such as:

- Private keys
- TLS client keys
- AWS credentials
- Authentication secrets

should never be committed to the repository.

Certificate and private-key directories should be excluded using `.gitignore`.

---

## Skills Demonstrated

This project demonstrates hands-on experience with:

- Network intrusion detection
- Suricata IDS configuration
- Custom IDS signature development
- MQTT protocol security
- IoT attack simulation
- Network packet capture
- PCAP analysis
- Python security scripting
- Docker networking
- Security event logging
- Elasticsearch log ingestion
- Git version control
- Security documentation

---

## Project Context

This project was developed as part of **CYT160 — IoT Security**.

It builds upon earlier work involving Dockerized MQTT security monitoring and TLS-secured MQTT communications, and focuses specifically on **attack simulation and Suricata threat detection**.

---

## Disclaimer

This project is intended strictly for educational, laboratory, and defensive security research purposes. All testing was performed against authorized systems in a controlled environment.
