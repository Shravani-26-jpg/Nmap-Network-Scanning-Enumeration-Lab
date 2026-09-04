# Nmap Network Scanning & Enumeration Lab

| Field | Details |
| :--- | :--- |
| **Student Name** | Shravani Karpe |
| **Roll Number** | 2267 |
| **Class** | Third Year CSE |
| **College** | JSPM JSCOE |
| **Subject** | Cybersecurity / Network Security |

---

## 1. Introduction

**Nmap (Network Mapper)** is an industry-standard, open-source security scanning tool utilized for network discovery, infrastructure mapping, and vulnerability assessment. It operates by generating custom raw IP packets to identify active network hosts, discover listening TCP/UDP ports, determine running application services and their exact version releases, fingerprint remote operating systems, and evaluate packet-filtering and firewall configurations.

This laboratory practical was performed inside an isolated and authorized virtual environment on **VMware Workstation**. The scanning platform was **Kali Linux** (`192.168.184.128`) executing **Nmap version 7.99**, targeting **Metasploitable 2** (`192.168.184.130`)—an intentionally vulnerable Linux virtual machine specifically maintained for cybersecurity testing, network scanning, and vulnerability research.

---

## 2. Objective

The primary objectives of this laboratory project were:
1. Understand the core principles and packet-level mechanics of Nmap scanning techniques.
2. Discover live, active systems across the local network subnet (`192.168.184.0/24`) without scanning ports.
3. Test Layer-3 network reachability, packet reliability, and round-trip latency using ICMP ping.
4. Perform default TCP port scanning to detect open services across the top 1000 standard ports.
5. Execute a full TCP Connect scan (`-sT`) using operating system socket calls.
6. Conduct targeted UDP scanning (`-sU`) on critical service ports (e.g., DNS port 53).
7. Execute TCP NULL (`-sN`) and FIN (`-sF`) scans to inspect target TCP/IP stack behavior against RFC 793 specifications.
8. Evaluate firewall and packet-filtering rules using TCP ACK scanning (`-sA`).
9. Fingerprint and detect the target operating system (`-O`) via TCP/IP stack behavior analysis.
10. Perform banner grabbing and service version detection (`-sV`) to uncover exact daemon releases.
11. Map out the target system's attack surface based strictly on empirical scan observations.

---

## 3. Tools & Technologies Used

| Tool / Technology | Role / Specification |
| :--- | :--- |
| **Kali Linux** | Security auditing and scanning platform (`192.168.184.128`) |
| **Metasploitable 2** | Target virtual machine with intentionally vulnerable services (`192.168.184.130`) |
| **VMware** | Virtualization hypervisor hosting the isolated private network |
| **Nmap 7.99** | Network discovery, port scanning, OS fingerprinting, and version detection tool |
| **Ping Utility** | ICMP echo-request network reachability verification tool |
| **Bash Terminal** | Command-line interface used for scan execution and log inspection |

---

## 4. Lab Environment

The laboratory environment was configured on an isolated VMware virtual private network segment with the following parameters:

- **Kali Linux**
- **Metasploitable 2**
- **VMware**
- **Nmap 7.99**
- **Kali IP:** `192.168.184.128`
- **Target IP:** `192.168.184.130`
- **Network:** `192.168.184.0/24` (`192.168.184.0-255`)
- **Target MAC Address:** `00:0C:29:45:D2:2C` (VMware)

---

## 5. Network Configuration / Diagram

Show the relationship:

```text
Kali Linux (192.168.184.128)
        |
        | Nmap Scan
        ↓
Metasploitable 2 (192.168.184.130)
```

```text
                      VMware Virtual Network
                         192.168.184.0/24
                                │
         ┌──────────────────────┴──────────────────────┐
         │                                             │
         ▼                                             ▼
┌───────────────────────────┐             ┌───────────────────────────┐
│        Kali Linux         │             │      Metasploitable 2     │
│       (Scanner VM)        │  Nmap Scan  │        (Target VM)        │
│                           │───────────► │                           │
│ IP:  192.168.184.128      │   Probes    │ IP:  192.168.184.130      │
│ OS:  Linux (Kali)         │             │ OS:  Linux 2.6.X          │
│ Nmap Version: 7.99        │             │ MAC: 00:0C:29:45:D2:2C    │
└───────────────────────────┘             └───────────────────────────┘
```

---

## 6. Practicals

All practicals below reflect the actual terminal sessions and exact outputs captured in the project screenshots.

---

## Practical 1 — Subnet Range Port Scan

### Explanation
This scan probes the entire network IP range (`192.168.184.0-255`) across default top ports to identify active machines on the subnet and simultaneously discover initial open TCP ports on responding systems.

### Command
```bash
nmap 192.168.184.0-255
```

### Scan Output
```text
Starting Nmap 7.99 ( https://nmap.org ) at 2026-08-17 23:45 -0400
Nmap scan report for 192.168.184.1
Host is up (0.00012s latency).
Not shown: 994 closed tcp ports (reset)
PORT     STATE SERVICE
135/tcp  open  msrpc
139/tcp  open  netbios-ssn
445/tcp  open  microsoft-ds
902/tcp  open  iss-realsecure
912/tcp  open  apex-mesh
7070/tcp open  realserver
MAC Address: 00:50:56:C0:00:08 (VMware)

Nmap scan report for 192.168.184.2
Host is up (0.00015s latency).
Not shown: 999 closed tcp ports (reset)
PORT   STATE SERVICE
53/tcp open  domain
MAC Address: 00:50:56:EA:34:C6 (VMware)

Nmap scan report for 192.168.184.130
Host is up (0.0024s latency).
Not shown: 977 closed tcp ports (reset)
PORT   STATE SERVICE
21/tcp open  ftp
22/tcp open  ssh
```

### Observation & Findings
- Subnet scanning identified active hosts on the segment:
  - `192.168.184.1` (VMware Virtual Adapter): 6 open ports (`135`, `139`, `445`, `902`, `912`, `7070`).
  - `192.168.184.2` (VMware Gateway/DNS): Port `53/tcp` open.
  - `192.168.184.130` (Metasploitable 2 target): Detected live with `21/tcp` (FTP) and `22/tcp` (SSH) open; 977 closed TCP ports responded with RST packets.

### Screenshot
![Subnet Range Port Scan](screenshots/01-ip-configuration.png)

---

## Practical 2 — Host Discovery (Ping Sweep)

### Explanation
Host discovery using the `-sn` flag (ping sweep) locates active hosts across a target network range without performing a port scan. This allows rapid mapping of live systems while generating minimal network traffic.

### Command
```bash
nmap -sn 192.168.184.0-255
```

### Scan Output
```text
Starting Nmap 7.99 ( https://nmap.org ) at 2026-08-17 23:48 -0400
Nmap scan report for 192.168.184.1
Host is up (0.00052s latency).
MAC Address: 00:50:56:C0:00:08 (VMware)
Nmap scan report for 192.168.184.2
Host is up (0.00055s latency).
MAC Address: 00:50:56:EA:34:C6 (VMware)
Nmap scan report for 192.168.184.130
Host is up (0.00076s latency).
MAC Address: 00:0C:29:45:D2:2C (VMware)
Nmap scan report for 192.168.184.254
Host is up (0.00059s latency).
MAC Address: 00:50:56:FC:4D:4F (VMware)
Nmap scan report for 192.168.184.128
Host is up.
Nmap done: 256 IP addresses (5 hosts up) scanned in 3.03 seconds
```

### Observation & Findings
- Scanned 256 IP addresses and discovered 5 live hosts in **3.03 seconds**:
  - `192.168.184.1` (MAC: `00:50:56:C0:00:08`, latency 0.00052s)
  - `192.168.184.2` (MAC: `00:50:56:EA:34:C6`, latency 0.00055s)
  - `192.168.184.130` (MAC: `00:0C:29:45:D2:2C`, latency 0.00076s) — Metasploitable 2
  - `192.168.184.254` (MAC: `00:50:56:FC:4D:4F`, latency 0.00059s)
  - `192.168.184.128` — Kali Linux Scanner host

### Screenshot
![Host Discovery](screenshots/02-host-discovery.png)

---

## Practical 3 — Specific Port Sweep Across Subnet (Port 80)

### Explanation
This scan queries a specific port (`-p 80`) across the entire subnet (`192.168.184.0-255`) to identify which systems on the network are actively hosting HTTP web servers.

### Command
```bash
nmap -p 80 192.168.184.0-255
```

### Scan Output
```text
Starting Nmap 7.99 ( https://nmap.org ) at 2026-08-17 23:51 -0400
Nmap scan report for 192.168.184.1
Host is up (0.00079s latency).

PORT   STATE  SERVICE
80/tcp closed http
MAC Address: 00:50:56:C0:00:08 (VMware)

Nmap scan report for 192.168.184.2
Host is up (0.00050s latency).

PORT   STATE  SERVICE
80/tcp closed http
MAC Address: 00:50:56:EA:34:C6 (VMware)

Nmap scan report for 192.168.184.130
Host is up (0.00041s latency).

PORT   STATE SERVICE
80/tcp open  http
MAC Address: 00:0C:29:45:D2:2C (VMware)

Nmap scan report for 192.168.184.254
Host is up (0.00041s latency).
```

### Observation & Findings
- Host `192.168.184.1`: Port `80/tcp` is **closed**.
- Host `192.168.184.2`: Port `80/tcp` is **closed**.
- Host `192.168.184.130` (Metasploitable 2): Port `80/tcp` is **open**, confirming that an active HTTP web service is operating on the target.

### Screenshot
![Port 80 Subnet Sweep](screenshots/port-scanning.png)

---

## Practical 4 — Network Reachability & Latency Test (ICMP Ping)

### Explanation
The `ping` command sends ICMP Echo Request packets to evaluate Layer-3 network reachability, quantify packet loss, and measure round-trip latency to the target machine.

### Command
```bash
ping 192.168.184.130
```

### Scan Output
```text
PING 192.168.184.130 (192.168.184.130) 56(84) bytes of data.
64 bytes from 192.168.184.130: icmp_seq=1 ttl=64 time=6.87 ms
64 bytes from 192.168.184.130: icmp_seq=2 ttl=64 time=1.27 ms
64 bytes from 192.168.184.130: icmp_seq=3 ttl=64 time=0.697 ms
64 bytes from 192.168.184.130: icmp_seq=4 ttl=64 time=1.12 ms
64 bytes from 192.168.184.130: icmp_seq=5 ttl=64 time=1.11 ms
64 bytes from 192.168.184.130: icmp_seq=6 ttl=64 time=0.490 ms
64 bytes from 192.168.184.130: icmp_seq=7 ttl=64 time=0.846 ms
64 bytes from 192.168.184.130: icmp_seq=8 ttl=64 time=1.65 ms
64 bytes from 192.168.184.130: icmp_seq=9 ttl=64 time=1.02 ms
64 bytes from 192.168.184.130: icmp_seq=10 ttl=64 time=7.28 ms
64 bytes from 192.168.184.130: icmp_seq=11 ttl=64 time=0.719 ms
64 bytes from 192.168.184.130: icmp_seq=12 ttl=64 time=0.789 ms
64 bytes from 192.168.184.130: icmp_seq=13 ttl=64 time=0.797 ms
64 bytes from 192.168.184.130: icmp_seq=14 ttl=64 time=0.867 ms
64 bytes from 192.168.184.130: icmp_seq=15 ttl=64 time=0.739 ms
64 bytes from 192.168.184.130: icmp_seq=16 ttl=64 time=0.683 ms
64 bytes from 192.168.184.130: icmp_seq=17 ttl=64 time=0.776 ms
64 bytes from 192.168.184.130: icmp_seq=18 ttl=64 time=1.78 ms
64 bytes from 192.168.184.130: icmp_seq=19 ttl=64 time=0.902 ms
64 bytes from 192.168.184.130: icmp_seq=20 ttl=64 time=1.61 ms
^Q64 bytes from 192.168.184.130: icmp_seq=21 ttl=64 time=1.05 ms
64 bytes from 192.168.184.130: icmp_seq=22 ttl=64 time=0.430 ms
64 bytes from 192.168.184.130: icmp_seq=23 ttl=64 time=0.913 ms
64 bytes from 192.168.184.130: icmp_seq=24 ttl=64 time=1.26 ms
```

### Observation & Findings
- Received 24 consecutive ICMP replies from `192.168.184.130` with 0% packet loss.
- `ttl=64` indicates a Linux operating system responding directly on the local link.
- Round-trip times remained between **0.430 ms** and **7.28 ms**, confirming excellent network connectivity.

### Screenshot
![Ping Test](screenshots/03-ping-test.png)

---

## Practical 5 — Default TCP Port Scan (Top 1000 Ports)

### Explanation
Scanning the target IP without specific flags probes the 1000 most common TCP ports. Ports returning SYN/ACK are identified as `open`, ports returning RST are reported as `closed`, and ports not replying or receiving ICMP filter errors are marked `filtered`.

### Command
```bash
nmap 192.168.184.130
```

### Scan Output
```text
Starting Nmap 7.99 ( https://nmap.org ) at 2026-08-18 00:12 -0400
Nmap scan report for 192.168.184.130
Host is up (0.0013s latency).
Not shown: 977 closed tcp ports (reset)
PORT     STATE SERVICE
21/tcp   open  ftp
22/tcp   open  ssh
23/tcp   open  telnet
25/tcp   open  smtp
53/tcp   open  domain
80/tcp   open  http
111/tcp  open  rpcbind
139/tcp  open  netbios-ssn
445/tcp  open  microsoft-ds
512/tcp  open  exec
513/tcp  open  login
514/tcp  open  shell
1099/tcp open  rmiregistry
1524/tcp open  ingreslock
2049/tcp open  nfs
2121/tcp open  ccproxy-ftp
3306/tcp open  mysql
5432/tcp open  postgresql
5900/tcp open  vnc
6000/tcp open  X11
6667/tcp open  irc
```

### Observation & Findings
- Discovered **21 open TCP ports** running on Metasploitable 2:
  - File transfer & remote access: FTP (`21`, `2121`), SSH (`22`), Telnet (`23`), VNC (`5900`), X11 (`6000`), r-services (`512`, `513`, `514`), backdoor shell (`1524`).
  - Network infrastructure: SMTP (`25`), DNS (`53`), HTTP (`80`), RPC (`111`), SMB/NetBIOS (`139`, `445`), NFS (`2049`).
  - Applications & databases: Java RMI (`1099`), MySQL (`3306`), PostgreSQL (`5432`), IRC (`6667`).
- **977 closed TCP ports** responded with RST packets (`reset`).

### Screenshot
![Port Scanning](screenshots/04-port-scanning.png)

---

## Practical 6 — TCP Connect Port Scan (`-sT`)

### Explanation
The TCP Connect scan (`-sT`) establishes a complete three-way TCP handshake (`SYN` -> `SYN/ACK` -> `ACK`) using the operating system's standard `connect()` system call. When a port is closed, the remote host rejects the connection attempt, which Nmap logs as `conn-refused`.

### Command
```bash
nmap -sT 192.168.184.130
```

### Scan Output
```text
Starting Nmap 7.99 ( https://nmap.org ) at 2026-08-18 00:16 -0400
Nmap scan report for 192.168.184.130
Host is up (0.0040s latency).
Not shown: 977 closed tcp ports (conn-refused)
PORT     STATE SERVICE
21/tcp   open  ftp
22/tcp   open  ssh
23/tcp   open  telnet
25/tcp   open  smtp
53/tcp   open  domain
80/tcp   open  http
111/tcp  open  rpcbind
139/tcp  open  netbios-ssn
445/tcp  open  microsoft-ds
512/tcp  open  exec
513/tcp  open  login
514/tcp  open  shell
1099/tcp open  rmiregistry
1524/tcp open  ingreslock
2049/tcp open  nfs
2121/tcp open  ccproxy-ftp
3306/tcp open  mysql
5432/tcp open  postgresql
5900/tcp open  vnc
6000/tcp open  X11
```

### Observation & Findings
- Target responded with **977 closed tcp ports (conn-refused)**, which confirms that full OS socket connections were attempted.
- Confirmed the same active listening services across the target's open ports.

### Screenshot
![TCP Connect Scan](screenshots/05-syn-scan.png)

---

## Practical 7 — UDP Port Scan (`-sU`)

### Explanation
The UDP scan (`-sU`) sends UDP packets to target ports. Since UDP is connectionless without handshakes, closed ports typically reply with an ICMP Port Unreachable message (Type 3, Code 3). Open UDP services return application-level replies or absorb probes silently.

### Command
```bash
nmap -sU -p 53 192.168.184.130
```

### Scan Output
```text
Starting Nmap 7.99 ( https://nmap.org ) at 2026-08-18 00:18 -0400
Nmap scan report for 192.168.184.130
Host is up (0.0024s latency).

PORT   STATE SERVICE
53/udp open  domain
MAC Address: 00:0C:29:45:D2:2C (VMware)

Nmap done: 1 IP address (1 host up) scanned in 0.73 seconds
```

### Observation & Findings
- UDP port `53/udp` responded as **open**, operating the DNS `domain` service.
- The scan completed in **0.73 seconds** for target MAC `00:0C:29:45:D2:2C`.

### Screenshot
![UDP Scan](screenshots/06-udp-scan.png)

---

## Practical 8 — TCP NULL Scan (`-sN`)

### Explanation
A TCP NULL scan sets no flags in the TCP header (all flag bits are 0). According to RFC 793, if an incoming packet has no flags set, a closed port must reply with an RST, whereas an open port must silently drop the packet. If no packet is returned, Nmap classifies the port as `open|filtered`.

### Command
```bash
nmap -sN 192.168.184.130
```

### Scan Output
```text
Starting Nmap 7.99 ( https://nmap.org ) at 2026-08-18 00:19 -0400
Nmap scan report for 192.168.184.130
Host is up (0.0022s latency).
Not shown: 977 closed tcp ports (reset)
PORT     STATE         SERVICE
21/tcp   open|filtered ftp
22/tcp   open|filtered ssh
23/tcp   open|filtered telnet
25/tcp   open|filtered smtp
53/tcp   open|filtered domain
80/tcp   open|filtered http
111/tcp  open|filtered rpcbind
139/tcp  open|filtered netbios-ssn
445/tcp  open|filtered microsoft-ds
512/tcp  open|filtered exec
513/tcp  open|filtered login
514/tcp  open|filtered shell
1099/tcp open|filtered rmiregistry
1524/tcp open|filtered ingreslock
2049/tcp open|filtered nfs
2121/tcp open|filtered ccproxy-ftp
3306/tcp open|filtered mysql
5432/tcp open|filtered postgresql
5900/tcp open|filtered vnc
6000/tcp open|filtered X11
```

### Observation & Findings
- **977 closed TCP ports** responded with RST packets (`reset`).
- All active listening ports dropped the flagless probes and were categorized as **`open|filtered`**, validating standard RFC 793 implementation on the target.

### Screenshot
![NULL Scan](screenshots/07-null-scan.png)

---

## Practical 9 — TCP FIN Scan (`-sF`)

### Explanation
The FIN scan transmits TCP packets with only the `FIN` flag set. Under RFC 793 rules, closed ports must respond with an RST packet, while listening open ports must silently ignore the unsolicited FIN packet. Ports that do not reply are designated as `open|filtered`.

### Command
```bash
nmap -sF 192.168.184.130
```

### Scan Output
```text
Starting Nmap 7.99 ( https://nmap.org ) at 2026-08-18 00:20 -0400
Nmap scan report for 192.168.184.130
Host is up (0.0027s latency).
Not shown: 977 closed tcp ports (reset)
PORT     STATE         SERVICE
21/tcp   open|filtered ftp
22/tcp   open|filtered ssh
23/tcp   open|filtered telnet
25/tcp   open|filtered smtp
53/tcp   open|filtered domain
80/tcp   open|filtered http
111/tcp  open|filtered rpcbind
139/tcp  open|filtered netbios-ssn
445/tcp  open|filtered microsoft-ds
512/tcp  open|filtered exec
513/tcp  open|filtered login
514/tcp  open|filtered shell
1099/tcp open|filtered rmiregistry
1524/tcp open|filtered ingreslock
2049/tcp open|filtered nfs
2121/tcp open|filtered ccproxy-ftp
3306/tcp open|filtered mysql
5432/tcp open|filtered postgresql
5900/tcp open|filtered vnc
6000/tcp open|filtered X11
```

### Observation & Findings
- **977 closed ports** replied with RST packets (`reset`).
- The listening services generated no response and were reported as **`open|filtered`**.

### Screenshot
![FIN Scan](screenshots/08-fin-scan.png)

---

## Practical 10 — TCP ACK Scan & Firewall Detection (`-sA`)

### Explanation
The TCP ACK scan (`-sA`) sends packets with only the `ACK` flag asserted. Because no active connection exists, any host not protected by a stateful packet-filtering firewall will reply with an `RST` packet, indicating the port is `unfiltered`. If a firewall blocks or drops the packet, the port remains `filtered`.

### Command
```bash
nmap -sA 192.168.184.130
```

### Scan Output
```text
Starting Nmap 7.99 ( https://nmap.org ) at 2026-08-18 00:21 -0400
Nmap scan report for 192.168.184.130
Host is up (0.0018s latency).
All 1000 scanned ports on 192.168.184.130 are in ignored states.
Not shown: 1000 unfiltered tcp ports (reset)
MAC Address: 00:0C:29:45:D2:2C (VMware)

Nmap done: 1 IP address (1 host up) scanned in 0.76 seconds
```

### Observation & Findings
- Output explicitly states: `Not shown: 1000 unfiltered tcp ports (reset)`.
- All 1000 probed ports returned RST packets, proving that packets pass directly to the operating system with **no active stateful firewall or packet filter** dropping traffic.

### Screenshot
![ACK Scan / Firewall Detection](screenshots/10-ack-scan.png)

---

## Practical 11 — Remote Operating System Detection (`-O`)

### Explanation
Nmap's OS detection engine (`-O`) inspects low-level TCP/IP stack behavior by sending specially constructed probe packets to open and closed ports and analyzing sequence number generation, TCP options, IP ID values, and window size behaviors against its fingerprint database.

### Command
```bash
nmap -O 192.168.184.130
```

### Scan Output
```text
Starting Nmap 7.99 ( https://nmap.org ) at 2026-08-18 00:24 -0400
Nmap scan report for 192.168.184.130
Host is up (0.0012s latency).
Not shown: 977 closed tcp ports (reset)
PORT     STATE SERVICE
21/tcp   open  ftp
22/tcp   open  ssh
23/tcp   open  telnet
25/tcp   open  smtp
53/tcp   open  domain
80/tcp   open  http
111/tcp  open  rpcbind
139/tcp  open  netbios-ssn
445/tcp  open  microsoft-ds
512/tcp  open  exec
513/tcp  open  login
514/tcp  open  shell
1099/tcp open  rmiregistry
1524/tcp open  ingreslock
2049/tcp open  nfs
2121/tcp open  ccproxy-ftp
3306/tcp open  mysql
5432/tcp open  postgresql
5900/tcp open  vnc
6000/tcp open  X11
6667/tcp open  irc
8009/tcp open  ajp13
8180/tcp open  unknown
MAC Address: 00:0C:29:45:D2:2C (VMware)
Device type: general purpose
Running: Linux 2.6.X
OS CPE: cpe:/o:linux:linux_kernel:2.6
OS details: Linux 2.6.9 - 2.6.33
Network Distance: 1 hop

OS detection performed. Please report any incorrect results at https://nmap.org/submit/ .
```

### Observation & Findings
- **Device type:** `general purpose`
- **Running:** `Linux 2.6.X`
- **OS CPE:** `cpe:/o:linux:linux_kernel:2.6`
- **OS details:** `Linux 2.6.9 - 2.6.33`
- **Network Distance:** `1 hop` (direct Layer-2 adjacency on virtual switch)
- Additionally revealed open ports `8009/tcp` (ajp13) and `8180/tcp` (unknown).

### Screenshot
![OS Detection](screenshots/12-os-detection.png)

---

## Practical 12 — Service & Application Version Detection (`-sV`)

### Explanation
Service and version detection (`-sV`) interrogates listening ports with application-level probes and parses banner responses to identify running software programs and their specific versions.

### Command
```bash
nmap -sV 192.168.184.130
```

### Scan Output
```text
Starting Nmap 7.99 ( https://nmap.org ) at 2026-08-18 00:28 -0400
Nmap scan report for 192.168.184.130
Host is up (0.00060s latency).
Not shown: 977 closed tcp ports (reset)
PORT     STATE SERVICE     VERSION
21/tcp   open  ftp         vsftpd 2.3.4
22/tcp   open  ssh         OpenSSH 4.7p1 Debian 8ubuntu1 (protocol 2.0)
23/tcp   open  telnet      Linux telnetd
25/tcp   open  smtp        Postfix smtpd
53/tcp   open  domain      ISC BIND 9.4.2
80/tcp   open  http        Apache httpd 2.2.8 ((Ubuntu) DAV/2)
111/tcp  open  rpcbind     2 (RPC #100000)
139/tcp  open  netbios-ssn Samba smbd 3.X - 4.X (workgroup: WORKGROUP)
445/tcp  open  netbios-ssn Samba smbd 3.X - 4.X (workgroup: WORKGROUP)
512/tcp  open  exec        netkit-rsh rexecd
513/tcp  open  login
514/tcp  open  tcpwrapped
1099/tcp open  java-rmi    GNU Classpath grmiregistry
1524/tcp open  bindshell   Metasploitable root shell
2049/tcp open  nfs         2-4 (RPC #100003)
2121/tcp open  ftp         ProFTPD 1.3.1
3306/tcp open  mysql       MySQL 5.0.51a-3ubuntu5
5432/tcp open  postgresql  PostgreSQL DB 8.3.0 - 8.3.7
5900/tcp open  vnc         VNC (protocol 3.3)
6000/tcp open  X11         (access denied)
6667/tcp open  irc         UnrealIRCd
8009/tcp open  ajp13       Apache Jserv (Protocol v1.3)
8180/tcp open  http        Apache Tomcat/Coyote JSP engine 1.1
MAC Address: 00:0C:29:45:D2:2C (VMware)
Service Info: Hosts: metasploitable.localdomain, irc.Metasploitable.LAN; OSs: Unix, Linux; CPE: cpe:/o:linux:linux_kernel

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 11.97 seconds
```

### Observation & Findings
- **vsftpd 2.3.4** on port `21/tcp` (known backdoor vulnerability CVE-2011-2523).
- **OpenSSH 4.7p1** on port `22/tcp`.
- **Apache httpd 2.2.8** on port `80/tcp`.
- **Samba smbd 3.X - 4.X** on ports `139/tcp` and `445/tcp`.
- **Metasploitable root shell** on port `1524/tcp`.
- **ProFTPD 1.3.1** on port `2121/tcp`.
- **MySQL 5.0.51a** on port `3306/tcp`.
- **PostgreSQL 8.3.0 - 8.3.7** on port `5432/tcp`.
- **UnrealIRCd** on port `6667/tcp`.
- **Apache Tomcat/Coyote JSP engine 1.1** on port `8180/tcp`.
- Internal domain names discovered: `metasploitable.localdomain` and `irc.Metasploitable.LAN`.

### Screenshot
![Service Version Detection](screenshots/13-service-version.png)

---

## Additional Scans (Status: No Screenshot Available)

To maintain strict project integrity, the following practical scans were not supported by terminal screenshot files in the repository:

### Practical — TCP SYN Stealth Scan (`-sS`)
- **Explanation:** SYN scanning sends SYN packets without completing the three-way handshake (`SYN` -> `SYN/ACK` -> `RST`), providing stealthier port identification.
- **Command:** `nmap -sS 192.168.184.130`
- **Status:** *Screenshot / result not available* (Practical 6 executed `nmap -sT` using full TCP Connect mode).

### Practical — TCP Xmas Scan (`-sX`)
- **Explanation:** Sets FIN, PSH, and URG flags ("lit like a Christmas tree") to evaluate RFC 793 compliance.
- **Command:** `nmap -sX 192.168.184.130`
- **Status:** *Screenshot / result not available*.

### Practical — Aggressive Scan (`-A`)
- **Explanation:** Combines OS detection (`-O`), version scanning (`-sV`), script scanning (`-sC`), and traceroute (`--traceroute`).
- **Command:** `nmap -A 192.168.184.130`
- **Status:** *Screenshot / result not available*.

### Practical — Version Intensity Scan (`--version-intensity`)
- **Explanation:** Controls version probe aggressiveness on a scale from 0 to 9.
- **Command:** `nmap -sV --version-intensity 9 192.168.184.130`
- **Status:** *Screenshot / result not available*.

---

## 7. Comprehensive Scan Results Summary

| Practical / Scan Type | Exact Command Visible | Target | Key Result / Observation | Local Screenshot |
| :--- | :--- | :--- | :--- | :--- |
| **Subnet Port Scan** | `nmap 192.168.184.0-255` | `192.168.184.0/24` | Identified hosts `.1`, `.2`, `.130` with initial listening ports | `screenshots/01-ip-configuration.png` |
| **Host Discovery** | `nmap -sn 192.168.184.0-255` | `192.168.184.0/24` | 5 active hosts detected in 3.03s; logged MAC addresses | `screenshots/02-host-discovery.png` |
| **Port Sweep (Port 80)** | `nmap -p 80 192.168.184.0-255` | `192.168.184.0/24` | Port 80 closed on `.1` & `.2`; open on `.130` (Metasploitable) | `screenshots/port-scanning.png` |
| **ICMP Ping Test** | `ping 192.168.184.130` | `192.168.184.130` | 24 packets replied; latency 0.43ms - 7.28ms; `TTL=64` | `screenshots/03-ping-test.png` |
| **Default Port Scan** | `nmap 192.168.184.130` | `192.168.184.130` | 21 open TCP ports found; 977 closed ports reset | `screenshots/04-port-scanning.png` |
| **TCP Connect Scan** | `nmap -sT 192.168.184.130` | `192.168.184.130` | Completed full 3-way handshake; 977 ports `conn-refused` | `screenshots/05-syn-scan.png` |
| **UDP Port Scan** | `nmap -sU -p 53 192.168.184.130` | `192.168.184.130` | Port `53/udp` open (ISC BIND domain service) in 0.73s | `screenshots/06-udp-scan.png` |
| **TCP NULL Scan** | `nmap -sN 192.168.184.130` | `192.168.184.130` | Open ports reported as `open\|filtered`; 977 closed ports reset | `screenshots/07-null-scan.png` |
| **TCP FIN Scan** | `nmap -sF 192.168.184.130` | `192.168.184.130` | Open ports reported as `open\|filtered`; 977 closed ports reset | `screenshots/08-fin-scan.png` |
| **ACK Scan / Firewall** | `nmap -sA 192.168.184.130` | `192.168.184.130` | 1000 ports unfiltered (reset); no active packet filtering | `screenshots/10-ack-scan.png` |
| **OS Detection** | `nmap -O 192.168.184.130` | `192.168.184.130` | Linux 2.6.X (Linux 2.6.9 - 2.6.33); distance 1 hop | `screenshots/12-os-detection.png` |
| **Service Versioning** | `nmap -sV 192.168.184.130` | `192.168.184.130` | Enumerated 23 service application versions & hostnames | `screenshots/13-service-version.png` |

---

## 8. Target Attack Surface & Security Analysis

Based strictly on the verified outputs from our Nmap scans against `192.168.184.130`, the target exhibits an extensive attack surface:

1. **Backdoor Shells & Remote Code Execution Risks:**
   - **Port 1524/tcp (`ingreslock`):** Running `Metasploitable root shell`, which grants an unauthenticated interactive root shell to anyone connecting over the network.
   - **Port 21/tcp (`vsftpd 2.3.4`):** Contains a notorious backdoor triggered by entering a smiley face `:)` in the username, opening a listener on TCP port 6200 (CVE-2011-2523).
   - **Port 6667/tcp (`UnrealIRCd`):** Contains the well-known `DEBUG_COMMAND` remote execution backdoor (CVE-2010-2075).

2. **Cleartext Transmission Protocols:**
   - **Port 23/tcp (`Linux telnetd`)** and **Ports 512/513/514/tcp (`rsh/rlogin`):** Transmit credentials and command data without encryption, allowing password harvesting via network sniffing.

3. **Exposed Database Services:**
   - **MySQL (`3306/tcp`)** and **PostgreSQL (`5432/tcp`)** are directly reachable over the network rather than restricted to `localhost` (`127.0.0.1`).

4. **Outdated Application Servers:**
   - **Apache 2.2.8 (`80/tcp`)** and **Apache Tomcat/Coyote 1.1 (`8180/tcp`)** are legacy releases subject to known vulnerabilities.

5. **Absence of Packet Filtering:**
   - The **ACK Scan (`-sA`)** confirmed that all 1000 ports returned RST packets, proving that no host-based firewall (e.g., `iptables`, `ufw`) is active to restrict unauthorized connection attempts.

---

## 9. Security Hardening & Remediation Recommendations

To remediate the vulnerabilities revealed during this practical exercise, a production Linux host should apply the following defensive practices:

1. **Service Minimization & Principle of Least Privilege:**
   - Terminate obsolete or unnecessary daemons (`vsftpd`, `telnetd`, `r-services`, and unauthenticated shells).
   - Replace unencrypted protocols with secure alternatives (replace Telnet and r-utilities with SSH; replace FTP with SFTP).

2. **Stateful Firewall Configuration:**
   - Configure a host-based firewall (`ufw` or `iptables`) with a default `DROP` policy:
     ```bash
     sudo ufw default deny incoming
     sudo ufw default allow outgoing
     sudo ufw allow 22/tcp
     sudo ufw enable
     ```
   - An active firewall converts the ACK scan result from `unfiltered` to `filtered`.

3. **Restricting Database Listeners:**
   - Bind MySQL and PostgreSQL to loopback (`127.0.0.1`) in `/etc/mysql/my.cnf` and `postgresql.conf`.

4. **Regular Patching & Updates:**
   - Upgrade software packages and operating system kernels to supported releases to mitigate known public exploits.

---

## 10. Conclusion & Ethical Disclaimer

This lab provided hands-on experience with network discovery, port enumeration, TCP/IP stack fingerprinting, firewall analysis, and service identification using Nmap 7.99 in a virtualized VMware environment. By analyzing responses from TCP Connect, UDP, NULL, FIN, and ACK probes, the target system's network exposure and attack surface were systematically identified.

> **Legal & Ethical Notice:** Network scanning and security testing must only be conducted on systems and networks with prior authorization from the owner. Unauthorized scanning may violate organizational security policies and applicable cyber laws. This project was conducted strictly within an isolated laboratory environment for educational purposes.
