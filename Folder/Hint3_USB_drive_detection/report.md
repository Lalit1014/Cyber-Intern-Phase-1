## Detection Use Case: USB Drive Detection

### Scenario Description
An external USB drive is connected to a system which could be used for data exfiltration or malware delivery.

### Objective
Detect USB drive insertions.

### Tools Used
- **SIEM**: ELK Stack
- **Log Source**: Sysmon
- **Lab Setup**: USB insertion on monitored Windows host.

### Event ID / Data Source Mapping
|Source|	Event ID / Field |	Description    |
|------|-------------------|-----------------|
|Sysmon|	     11	         | File created    |
|Winlog|       6416        | Drive Detection |

### Detection Logic / Query
```dsl
event.code:11 or event.code:6416
```
The above query filters all the files created and by analysing the details, the file can be seen copied from USB to the Windows Machine. This detection combines Sysmon Event ID 11 (file creation) and Windows Event ID 6416 (Plug and Play device connection) to monitor USB activity. It helps identify when a USB device is connected and files are created or accessed, indicating potential data transfer or malware execution.
