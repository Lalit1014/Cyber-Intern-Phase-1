# Brute Force Attack

## Scenerio Description
An attacker attempts several unsuccessful login attempts on a Windows system using an Brute-force method to gain unauthorized access.

### Objective
Detect Seven or more failed login attempts (Event ID 4625) from the same host within a 2-minute period.

### Tools Used
- **SIEM**: Elastic Stack (Kibana)
- **Log Source**: Windows Event Logs (via Winlogbeat)
- **Lab Setup**: Windows 10 machine sending logs to Elastic Cloud; multiple failed login attempts simulated using incorrect credentials.

### Event ID / Data Source Mapping
| Source        | Event ID / Field         | Description            |
|---------------|--------------------------|------------------------|
| Windows Logs  | 4625 / winlog.event_data.IpAddress | Failed login attempt |

### Detection Logic / Query
#### Elastic Security Threshold Rule (Kibana)
```json
{
  "rule_type": "threshold",
  "event_category": "authentication",
  "index": ["winlogbeat-*"],
  "event_id": "4625",
  "threshold": {
    "field": ["host.name"],
    "value": 3
  },
  "time_window": "2m",
  "query": "event.code:4625 AND winlog.event_data.IpAddress:*"
}
```
The above query is executed when multiple failed logins exceeding count of Three are detected within 2 minutes. By analysing the logs we can find the user name and the number of attempts taken to try and bruteforce the password.
