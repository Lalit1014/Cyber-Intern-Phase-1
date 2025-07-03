## Detection Use Case: Suspicious PowerShell Execution

### Scenario Description
An attacker uses encoded or obfuscated PowerShell scripts to execute malicious commands.

### Objective
Identify the use of encoded PowerShell commands.

### Tools Used
- **SIEM**: ELK Stack
- **Log Source**: Sysmon
- **Lab Setup**: Sysmon logging process creation.

### Event ID / Data Source Mapping
|Source| Event ID / Field|	Description    |
|------|-----------------|-----------------|
|Sysmon|        1        | Process creation|

### Detection Logic / Query
```Kibana
event.code:1 AND process.name:"powershell.exe" 
```
The above query filters all processes created with the name powershell.exe. By analysing each field through Sysmon or Kibana we can find the smoch.exe has been executed and also the powershell loga for the powershell command executed. This query detects when PowerShell (`powershell.exe`) is executed on the system, using Sysmon Event ID 1 which logs process creation. It's useful for identifying suspicious PowerShell activity, often used by attackers for script-based attacks, fileless malware, or system reconnaissance.

