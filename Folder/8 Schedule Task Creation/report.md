## Detection Use Case: Scheduled Task Creation

## Scenario Description
Attackers create scheduled tasks for persistence or delayed execution of malware.

## Objective
Detect the creation of scheduled tasks.

## Tools Used
- **SIEM**: ELK Stack
- **Log Source**: Windows Event Logs
- **Lab Setup**: Task Scheduler usage logged and monitored.

### Event ID / Data Source Mapping
|   Source	 |Event ID / Field|	    Description       |
|------------|----------------|-----------------------|
|Windows Logs|	    4698	    | Scheduled task created|

### Detection Logic / Query
```dsl
event.code:4698 AND winlog.event_data.TaskName:*
```
