## Detection Use Case: Fileless Attack

### Scenario Description
Attackers use fileless techniques like memory-based payloads or script-based execution without dropping files.

### Objective
Detect suspicious PowerShell or WMI execution patterns.

### Tools Used
- **SIEM**: ELK Stack
- **Log Source**: Sysmon
- **Lab Setup**: Fileless script run using PowerShell.

### Event ID / Data Source Mapping
|Source|	Event ID / Field |	Description    |
|------|-------------------|-----------------|
|Sysmon|	        1	       | Process creation|

### Detection Logic / Query
```dsl
event.code:1 AND process.name:powershell.exe AND process.command_line.text:(*IEX* OR *Invoke-Expression*)
```
This detection identifies fileless attacks where PowerShell is used to execute code directly from memory using commands like IEX or Invoke-Expression. 
This kind of behavior is commonly used by attackers to bypass disk-based defenses and avoid leaving eevidence on the file system.
