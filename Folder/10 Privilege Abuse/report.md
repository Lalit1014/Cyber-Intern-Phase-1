## Detection Use Case: Privilege Escalation Attempt

## Scenario Description
An attacker attempts to escalate privileges using techniques like token impersonation or SeDebugPrivilege.

## Objective
Detect usage of powerful privileges or abnormal token impersonation.

## Tools Used
- **SIEM**: ELK Stack
- **Log Source**: Sysmon
- **Lab Setup**: Simulated escalation using Metasploit or token tools.

### Event ID / Data Source Mapping
|Source	    |Event ID / Field|	Description                |
|-----------|----------------|-----------------------------|
|Windows Log|	      4720	   |User account creation        |
|Windows Log|       4732     |Added to Administrators group|

Detection Logic / Query
```dsl
event.code: "4720" OR event.code: "4732"
```
