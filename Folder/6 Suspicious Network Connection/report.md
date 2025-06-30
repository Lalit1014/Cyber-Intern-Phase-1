## Detection Use Case: Suspicious Network Activity

### Scenario Description
An attacker establishes a suspicious or unauthorized outbound connection from the victim system.

### Objective
Detect connections to non-standard ports or suspicious IPs.

### Tools Used
- **SIEM**: ELK Stack
- **Log Source**: Sysmon
- **Lab Setup**: Connection to attacker IP via Netcat.

### Event ID / Data Source Mapping
|Source|	Event ID / Field |	Description           |
|------|-------------------|------------------------|
|Sysmon|	       3	       | Network connection made|

### Detection Logic / Query
```dsl
event.code:'3'
```
This query uses **Sysmon Event ID 3** to detect network connections initiated by a process. It helps identify suspicious outbound connections, such as those to unusual IP addresses or non-standard ports, which may indicate data exfiltration, C2 communication, or lateral movement.
