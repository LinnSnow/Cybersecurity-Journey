# Log Analyzer

A Python script that parses authentication logs and flags suspicious patterns — brute-force login attempts, repeated failures from the same source, and other anomalies a SOC analyst would triage.

## What it does
- Reads log files line by line and parses timestamp, source, event type, and outcome
- Flags patterns like repeated failed logins from the same IP within a short time window
- Outputs a summary of flag ged events for review

## How to run it
```bash
python log_analyzer.py sample_logs/auth.log
```

## What I learned
- Tracking the past activities of the same IP address to identify a Brute Force attack not through manual skimming but actual commands to filter data and flag suspicious behaviour disguised as normal traffic
- How a system should react instantly by flagging any repetitive attempts from a IP address or at the task of some source giving root privileges and whether the source should be able to give such privileges to someone else. 

## Sample data
Uses synthetic/sample log data — no real IPs, credentials, or production data.

## Tools
Python