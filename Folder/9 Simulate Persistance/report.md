## Detection Use Case: Simulated Persistence Mechanism
### Scenario Description
An attacker uses common persistence techniques like adding a binary to startup or modifying scheduled tasks.

### Objective
Detect indicators of persistence setups on Windows hosts.

### Tools Used
- **SIEM**: ELK Stack
- **Log Source**: Sysmon & Windows Logs
- **Lab Setup**: Startup folder and registry-based persistence manually configured.

### Event ID / Data Source Mapping
|Source	     | Event ID / Field|	Description          |
|------------|-----------------|-----------------------|
|Sysmon	     |       13	       |Registry modification  |
|Windows Logs|	    4698	     |Scheduled task creation|

### Detection Logic / Query
```dsl
event.code:"13" and event.code:"4698"
```
