# Ping & Discovery

NMAP output

{% code overflow="wrap" %}
```bash
# Format:
nmap [flags] [output_flag] <save_name>
nmap [flags] [output_flag] > /xxx/xxx/<save_name>

# Outputs
-oN    #NMAP output, simple text.
-oG    #Grepable output, consists of comments (lines starting with a pound (#)) and target lines.
-oX    # XML output: includes a document type definition (DTD) which allows XML parsers to validate Nmap XML output. XML offers a stable format that is easily parsed by software. Is far more powerful than grepable format, and is nearly as convenient for experienced users.
-oA    # All formats.
```
{% endcode %}



NMAP discovery

{% code overflow="wrap" %}
```bash
# Fast Discovery: T4 o T5 (--min-rate 5000)
nmap -sn -n -T4 -v <IP> -oN <save.file> # save in readable plain text.
nmap -sn -n -T4 -v <IP> -oA <save.file> # Save all formats
nmap -sn -n -T4 -v <IP> -oX <save.file> # Save XML format
# -sn: No ports scan
# -n: no dns resolve

# Comprehensive Scan to detect alive hosts
nmap -n -sn -PE -PP -PS21,22,23,25,80,113,443,31339 --source-port 53 -T4 -vv <IP> -oN <save.file>
# -PE: ICMP echo request (standar ping query) - code type 8, echo reply - code 0
# -PP: timestamp request - code 13 -timestamp reply code14
# information request - type 15 (nmap no lo implementa más)
# -PM: address mask request - type 17 -reply code 18

# When Firewall is active: PA, ACK probes - Beware of false positives if the FW is not active
nmap -n -sn -PE -PP -PS21,22,23,25,80,113,443,31339 -PA80,113,443,10042 --source-port 53 -T4 -vv <IP> -oN <save.file>

# Discovery of ALL ports and services without ICMP, without DNS and without versions
# Use this scan if the host is already known to be alive, eg. CTF or auditing that does not require auditing the ICMP response from hosts. If you require the latter, start with host discovery
nmap -p- --open -sS -T4 -vv -Pn -n <IP> -oN <save.file>
```
{% endcode %}
