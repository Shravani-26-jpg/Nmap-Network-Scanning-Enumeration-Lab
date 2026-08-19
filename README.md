# Nmap Network Scanning & Enumeration Lab

## Introduction

Nmap (Network Mapper) is an open-source network scanning and security auditing tool used for network discovery, port scanning, service identification, operating system detection, and security assessment.

This project demonstrates practical network scanning and enumeration using Nmap in an isolated virtual laboratory environment consisting of Kali Linux as the scanning machine and Metasploitable 2 as the target machine.

The practical covers host discovery, port scanning, service and version detection, OS detection, NSE script scanning, UDP scanning, and firewall/packet-filtering analysis.

---

## Objective

The objectives of this practical are:

- To understand the fundamentals of network scanning using Nmap.
- To identify active hosts on a network.
- To identify open, closed, and filtered ports.
- To detect running services and their versions.
- To perform operating system detection.
- To perform NSE script scanning.
- To understand TCP and UDP scanning techniques.
- To analyze firewall and packet-filtering behavior.
- To generate and document Nmap scan results.

---

## Tools & Technologies Used

| Tool | Purpose |
|------|---------|
| Kali Linux | Scanning and security testing platform |
| Nmap 7.99 | Network scanning and enumeration |
| Metasploitable 2 | Intentionally vulnerable target machine |
| VMware | Virtualization environment |
| Terminal | Executing Nmap commands |

---

## Lab Environment

The practical was performed in an isolated VMware virtual network.

| Machine | Role | IP Address |
|---------|------|------------|
| Kali Linux | Nmap Scanner | 192.168.184.128 |
| Metasploitable 2 | Target VM | 192.168.184.130 |

Network:

```text
192.168.184.0/24

## Network Topology

                    VMware Virtual Network
                             |
                             |
                       192.168.184.0/24
                             |
                 ------------+------------
                 |                         |
                 |                         |
          Kali Linux              Metasploitable 2
          Nmap Scanner                 Target
          192.168.184.128          192.168.184.130
                 |                         |
                 |------ Nmap Scan --------|

## Practical 1 — Host Discovery

### Command

nmap -sn 192.168.184.0/24

### Result

The scan identified active hosts in the network.

### Screenshot
<img width="712" height="516" alt="nmap 2 host scan" src="https://github.com/user-attachments/assets/4a4c41e1-3341-407e-a6f7-0b91146c9b5c" />


### Practical 2

```markdown
## Practical 2 — Port Scanning

### Explanation

Port scanning is used to identify open TCP ports on the target system. Open ports can indicate services that are available on the target.

### Command

```bash
nmap 192.168.184.130
### Screenshot
<img width="646" height="478" alt="nmap 5" src="https://github.com/user-attachments/assets/571b3dfb-bd9f-4e4d-88e4-1ded0d19f8c7" />


### Practical 3

```markdown
## Practical 3 — Service & Version Detection

### Explanation

Service and version detection helps identify which services are running on open ports and attempts to determine their software versions.

### Command

```bash
nmap -sV 192.168.184.130
### Screenshot
<img width="653" height="620" alt="nmap 14" src="https://github.com/user-attachments/assets/f9b468b6-8a64-454f-8c5c-89297858b2f1" />


### Practical 4

```markdown
## Practical 4 — OS Detection

### Explanation

OS detection attempts to identify the operating system of the target by analyzing its responses to Nmap probes.

### Command

```bash
sudo nmap -O 192.168.184.130
### Screenshot
<img width="597" height="652" alt="nmap 12" src="https://github.com/user-attachments/assets/b0e8b2ca-ad74-4926-a68c-4cbca261f627" />

## Practical 5 — TCP SYN Scan

### Explanation

A TCP SYN scan is used to identify open TCP ports on the target system by sending SYN packets and analyzing the responses. It is one of the commonly used Nmap scanning techniques.

### Command

```bash
nmap -sS 192.168.184.130
### Screenshot
<img width="702" height="486" alt="nmap 6" src="https://github.com/user-attachments/assets/3128ef10-1682-4779-ac57-a3937ce76009" />


### Practical 6

```markdown
## Practical 6 — Firewall Detection

### Explanation

An ACK scan can be used to analyze how a target's firewall or packet-filtering mechanism handles TCP packets.

### Command

```bash
sudo nmap -sA 192.168.184.130
### Screenshot
<img width="677" height="270" alt="nmap 11" src="https://github.com/user-attachments/assets/2b702d42-82ed-4617-82c0-5647ebbb1f29" />


### Practical 7 — Scan Report

```markdown
## Practical 7 — Nmap Scan Report

### Explanation

A combined Nmap scan can perform service detection, OS detection, and default NSE script scanning. The output can also be saved to a text file for documentation.

### Command

```bash
sudo nmap -sC -sV -O 192.168.184.130 -oN scan-report.txt
