Question Bank - Akamai SRE (HackerRank)

Total Questions: 25

---

Linux

Q001. What is the fundamental difference between the "fork()" and "exec()" system calls?

Options

A. "fork()" runs a program in the background; "exec()" runs it in the foreground.

B. "fork()" terminates a process; "exec()" restarts it.

C. "fork()" creates a new process, while "exec()" replaces the current process image with a new program, keeping the same PID.

D. They both create a new process, but "exec()" copies the parent's memory.

Answer

C

Notes

"fork()" creates a new process.

"exec()" replaces the current process image.

---

Q002. When troubleshooting Out-of-Memory (OOM) issues, which command sequence is used to check the kernel logs for evidence of the OOM killer?

Options

A. "df -h"

B. "ss -tulpn"

C. "dmesg | grep -i "out of memory""

D. "uptime"

Answer

C

---

Q003. If you delete the target file of a soft link (symlink), what happens to the link and the data?

Options

A. The soft link breaks (becomes a dangling pointer), and the data is lost if no other hard links exist.

B. The link becomes a hard link to the data.

C. The data remains because the soft link acts as a backup.

D. The original file's inode number is transferred to the soft link.

Answer

A

---

Q004. In the Linux load average output, what does the load average fundamentally measure?

Options

A. The total amount of memory consumed by all running processes.

B. The percentage of CPU utilization over the last 15 minutes.

C. The average number of processes waiting for CPU or I/O over time.

D. The number of active user sessions connected to the server.

Answer

C

---

Q005. Which TCP state indicates that the connection has been fully closed, but the socket is kept around briefly to catch stray packets?

Options

A. LISTEN

B. ESTABLISHED

C. CLOSE_WAIT

D. TIME_WAIT

Answer

D

---

Q006. Which directory is primarily used for storing volatile runtime data since boot, such as PID files and sockets, and is cleared on reboot?

Options

A. /etc

B. /var

C. /home

D. /run

Answer

D

---

Q007. Which command chain is used to find the top 5 most frequent error messages in a log file named "app.log"?

Options

A. "tail -5 app.log | uniq -c"

B. "grep -i "error" app.log | sort | uniq -c | sort -nr | head -5"

C. "awk '/error/ {print $0}' app.log | head -5"

D. "cat app.log | sort -nr | head -5"

Answer

B

---

Q008. When a Linux system performs DNS resolution, which file is checked before a query is sent to the nameserver specified in "/etc/resolv.conf"?

Options

A. "/etc/nsswitch.conf"

B. "/etc/hosts"

C. "/etc/sysconfig/network"

D. "/var/log/messages"

Answer

B

---

Q009. Which systemd directives ensure a service is automatically restarted if it crashes or terminates abnormally?

Options

A. "Restart=always"

B. "Restart=on-failure" or "Restart=on-abnormal"

C. "WantedBy=multi-user.target"

D. "ExecStartPost=/bin/true"

Answer

B

---

Q010. Which firewall rule in iptables would drop all incoming traffic except SSH only from "192.168.1.0/24"?

Answer

iptables -A INPUT -p tcp --dport 22 -s 192.168.1.0/24 -j ACCEPT
iptables -P INPUT DROP

---

Networking & Security

Q011. A switch CAM table becomes full due to malicious traffic. What attack has occurred?

Options

A. Broadcast flood

B. MAC spoofing / MAC flooding

C. DNS poisoning

D. MITM attack

E. DDoS SYN flood attack

Answer

B

---

Q012. Which transport protocol is used in VoIP communication and why?

Options

A. TCP because it is connection oriented and uses less bandwidth

B. UDP because it is connection oriented and uses less bandwidth

C. UDP because it is connectionless oriented and uses less bandwidth

D. TCP because it is connectionless oriented and uses less bandwidth

Answer

C

---

Q013. The development team is asking you to set up a lab network for a payment gateway application. Users connect to the backend using HTTPS on default ports. What is the minimum port configuration required?

Options

A. Client: 443. Server: outgoing ports.

B. Client: outgoing ports. Server: 443.

C. Client: outgoing ports. Server: 80.

D. Client: 23. Server: outgoing ports.

Answer

B

---

Q014. If the maximum number of bits available in the sequence number is K, what is the maximum window size?

Options

A. K − 1

B. K + 1

C. (2 × 2 × 2 ... K terms) − 1

D. (2 × 2 × 2 ... K terms) + 1

Answer

C ("2^K - 1")

---

Q015. In the following pairs of OSI protocol layers/sub-layers and functionalities, which pair is incorrect?

Options

A. Network layer and Routing

B. Datalink layer and Node to node delivery

C. Physical layer and Bit Synchronization

D. Datalink control sublayer and channel sharing

Answer

D

---

Q016. If 5 devices are in a network, what is the number of cable links required for a mesh and for a ring topology?

Options

A. 15 and 5

B. 10 and 5

C. 15 and 4

D. 10 and 4

Answer

B

---

Q017. A mid-size organization is experiencing intermittent network connectivity issues. Preliminary analysis suggests the problem might not be hardware related. Which measure should be considered FIRST?

Options

A. Implement strict firewall rules to block all traffic temporarily.

B. Review network traffic logs and patterns.

C. Upgrade all network device firmware.

D. Restrict configuration access to senior IT staff only.

Answer

B

---

Q018. While configuring VLANs on a network switch, certain VLANs are unable to communicate with each other. Assuming interfaces are correctly assigned, what could be the reason?

Options

A. The switch is not VLAN capable.

B. Inter-VLAN routing is not configured on the router or Layer 3 switch.

C. The cables are not plugged in.

D. The VLAN IDs are conflicting.

Answer

B

---

Q019. Which of the following statements is incorrect about Kubernetes CronJobs?

Options

A. CronJobs are useful for periodic and recurring tasks.

B. Jobs should be idempotent.

C. There are circumstances where two jobs might be created, or no job might be created.

D. All of the statements are correct.

Answer

D

---

Q020. As part of a Kubernetes cluster development, you need to schedule a periodic job that executes an API remote call. What is the correct definition?

Answer

kubectl create cronjob api \
  --image=busybox \
  --schedule="*/1 * * * *" \
  -- wget -qO- https://api.hacker-application.com/update

---

Q021. A company's database server has irregular connectivity issues. Which action is most appropriate to identify and resolve the issue?

Options

A. Execute "netstat -an | grep [DatabaseServerIP]"

B. Run "traceroute [DatabaseServerIP]" from various network endpoints.

C. Capture packets with "tcpdump" on the router.

D. Increase IDS surveillance on database traffic.

Answer

B

---

Q022. You move nginx and postfix behind a cloud security provider and change their IP addresses. Which DNS entries need to be changed?

Options

A. A, NS, MX

B. NS, MX

C. NS, SOA

D. SOA

E. A

F. A, MX

G. A, MX, SOA, NS

Answer

F

---

Hands-On Questions

Q023. Kubernetes StatefulSet

Problem Statement

There is a Kubernetes namespace called "hacker-company" with a Service named "nginx":

- Namespace: "hacker-company"
- Service name: "nginx"
- Label selector: "role=frontend"
- Port: "80"
- Headless service ("clusterIP: None")

Complete "definition.yml" so that:

- A StatefulSet named "frontend" is created.
- Namespace is "hacker-company".
- Service name is "nginx".
- Replicas = "2".
- Container name = "nginx".
- Image = "nginx:latest".
- Container port = "80".
- Labels and selectors match the Service.

---

Q024. Python Frequency Analyzer

Problem Statement

Implement:

get_top_letters_and_words(text, num)

Requirements:

- Lowercase and uppercase are treated the same.
- Punctuation is removed.
- Contractions such as ""don't"" become:
  - ""don""
  - ""t""
- Count:
  - letter frequencies
  - word frequencies
- Sort:
  1. frequency descending
  2. alphabetical ascending
- Return the top "num" letters and words.

---

Q025. Linux System Resource Log Analysis

Problem Statement

File:

/tmp/1671724-linux-analyzing-system-resource-usage/system_resources.log

Format:

USERNAME PROCESS_ID CPU_USAGE MEMORY_USAGE

Requirements:

- Identify processes with:
  - CPU > 30%
  - OR Memory > 100 MB
- Sort results by:
  - PROCESS_ID ascending
- Output:

USERNAME PROCESS_ID CPU_USAGE MEMORY_USAGE

---

Answer Key

Question| Answer
Q001| C
Q002| C
Q003| A
Q004| C
Q005| D
Q006| D
Q007| B
Q008| B
Q009| B
Q010| SSH allow + INPUT DROP
Q011| B
Q012| C
Q013| B
Q014| C
Q015| D
Q016| B
Q017| B
Q018| B
Q019| D
Q020| kubectl create cronjob
Q021| B
Q022| F
Q023| StatefulSet implementation
Q024| Python implementation
Q025| Log analysis implementation