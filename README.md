# Snort Intrusion Detection Lab

## Overview

This lab demonstrates how access control warnings can be generated and analyzed using an intrusion detection system (IDS) in Kali Linux, with Snort configured to monitor network traffic and alert on suspicious activity.

## Environment

- Kali Linux
- Snort (IDS)

## What I Did

### Step 1: Identify the Network Interface
Ran `ip a` to inspect the system's network interfaces, identifying the primary interface (`eth0`) and retrieving its IPv4 address for use in traffic monitoring.

### Step 2: Validate the Snort Configuration
Opened the Snort configuration file with `sudo nano /etc/snort/snort.conf`, then checked that the configuration was valid using:
```
sudo snort -c /etc/snort/snort.lua -T
```

### Step 3: Add a Custom Detection Rule
Opened the local rules file with `sudo nano /etc/snort/rules/local.rules` and added the following rule to detect ICMP traffic:
```
alert icmp any any -> any any ( msg:"Access Control Warning: ICMP Traffic Detected"; sid:1000002; rev:1; )
```

### Step 5: Enable the Local Rules in Snort's Configuration
Opened `sudo nano /etc/snort/snort.lua` and added the following block to enable the built-in rules along with the custom local rules:
```
ips =
{
    enable_builtin_rules = true,
    rules = [[
        include /etc/snort/rules/local.rules
    ]]
}
```

### Step 6: Revalidate the Configuration
Re-ran `sudo snort -c /etc/snort/snort.lua -T` to confirm the updated configuration was still valid.

### Step 7: Run Snort
Started Snort in a first terminal to actively monitor traffic:
```
sudo snort -c /etc/snort/snort.lua -i lo -A alert_fast -k none
```

### Step 8: Trigger the ICMP Rule
In a second terminal, ran `ping -c 4 127.0.0.1` to generate ICMP traffic and trigger the custom rule. Snort successfully detected the traffic and produced an "Access Control Warning: ICMP Traffic Detected" alert in the first terminal, confirming the IDS was working as configured.

## Existing vs. Improved Warning Systems

**Existing Warning Systems**
- Intrusion Detection Systems (IDS), e.g., Snort
- Monitor network traffic in real time
- Detect suspicious patterns (e.g., repeated access attempts)
- Generate alerts based on predefined rules

**Potential Improvements**
- Detect anomalies instead of relying only on static rules
- Identify unusual login patterns or access behavior
- Reduce false positives

## Key Takeaways

- Rule-based IDS tools like Snort are effective at flagging known, predefined traffic patterns (like ICMP floods) in real time, but their coverage is only as good as the rules configured.
- Validating configuration changes (`-T` flag) before running Snort live is an important step to catch syntax errors early.
- Purely rule-based detection has limits — it can generate false positives and won't catch novel attack patterns, which is where anomaly-based detection could improve on traditional signature-based IDS.
