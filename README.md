# Network Scanning Using Nmap

A practical cybersecurity project demonstrating host discovery, TCP port
scanning, service detection, operating-system fingerprinting, NSE
scripting, and TCP filtering analysis using Nmap.

> **Privacy note:** IP addresses have been redacted in this report.
> Replace `TARGET_IP` with the address of your authorized lab target
> when running commands. The scan results below reflect the observations
> recorded during this lab.

## 1. Project Overview

  -----------------------------------------------------------------------
  Item                                Details
  ----------------------------------- -----------------------------------
  Project                             Network Scanning Using Nmap

  Scanner                             Kali Linux

  Nmap version                        7.98

  Target                              Windows host's VMware NAT adapter

  Target IP                           `[REDACTED]`

  Network environment                 Personal VMware lab

  Purpose                             Discover the target, inspect ports
                                      and services, estimate the OS, run
                                      an NSE script, and examine
                                      filtering behavior
  -----------------------------------------------------------------------

## 2. Objectives

-   Perform host discovery.
-   Scan TCP ports and interpret their states.
-   Detect services and attempt version identification.
-   Attempt operating-system detection.
-   Run a safe informational NSE script.
-   Examine TCP filtering behavior using an ACK scan.
-   Record findings, limitations, and conclusions.

## 3. Lab Setup

-   Kali Linux VM with Nmap 7.98 installed.
-   Windows 11 host using a VMware NAT virtual adapter.
-   Scanning was limited to the user's own lab target.

The target and scanner IP addresses are intentionally omitted from this
public report.

## 4. Host Discovery

**Command template:**

``` bash
nmap -sn TARGET_IP
```

**Observed result:**

``` text
Host is up (approximately 0.00037s latency).
MAC Address: [REDACTED] (VMware)
1 IP address (1 host up) scanned.
```

**Finding:** Nmap reported the target as up. The MAC address was
associated with VMware.

## 5. TCP Port Scanning

**Command template:**

``` bash
nmap -Pn TARGET_IP
```

**Observed result:**

``` text
PORT     STATE     SERVICE
3306/tcp open      mysql

999 filtered TCP ports (no-response)
```

**Finding:** TCP port 3306 was reported open and associated with MySQL.
The other 999 scanned TCP ports were reported as filtered.

**Interpretation:** A filtered result means Nmap could not determine the
port's state because it did not receive a response that resolved the
state. It does not prove that every filtered port is closed.

## 6. Service and Version Detection

**Command template:**

``` bash
nmap -sV -Pn -p 3306 TARGET_IP
```

**Observed result:**

``` text
PORT     STATE SERVICE VERSION
3306/tcp open  mysql   MySQL (unauthorized)
```

**Finding:** Nmap associated TCP port 3306 with MySQL, but did not
identify an exact version.

**Limitation:** This result alone does not establish which process or
machine is providing the service. The service identity should be
verified before drawing conclusions.

## 7. Operating-System Detection

**Command template:**

``` bash
sudo nmap -O -Pn TARGET_IP
```

**Observed result:**

``` text
Warning: OSScan results may be unreliable because we could not find
at least 1 open and 1 closed port.

Aggressive OS guess:
Microsoft Windows 11 21H2 (91%)

No exact OS matches for host.
Network Distance: 1 hop
```

**Finding:** Nmap suggested Windows 11 21H2 with 91% confidence.

**Limitation:** Nmap explicitly warned that the OS result might be
unreliable because it could not find at least one open and one closed
port. Treat this as an estimate, not a confirmed identification.

## 8. NSE Script Scanning

**Command template:**

``` bash
nmap -Pn -p 3306 --script=mysql-info TARGET_IP
```

**Observed result:**

``` text
PORT     STATE SERVICE
3306/tcp open  mysql
```

**Finding:** The port was reported open, but the script did not return
additional MySQL information.

**Limitation:** No extra script output does not establish that the
service is vulnerable or secure.

## 9. Firewall and Filtering Analysis

**Command template:**

``` bash
sudo nmap -sA -Pn -p 3306,80,443,445,3389 TARGET_IP
```

**Observed result:**

``` text
PORT     STATE     SERVICE
80/tcp   filtered  http
443/tcp  filtered  https
445/tcp  filtered  microsoft-ds
3306/tcp filtered  mysql
3389/tcp filtered  ms-wbt-server
```

**Finding:** All five tested ports were reported as filtered during the
TCP ACK scan.

**Interpretation:** This suggests that packet filtering may be affecting
the tested traffic.

**Limitation:** An ACK scan cannot independently prove that a particular
firewall is present or identify the device responsible for filtering.

## 10. Results Summary

  -----------------------------------------------------------------------
  Test                    Observation             Status
  ----------------------- ----------------------- -----------------------
  Host discovery          Target reported up      Completed

  TCP port scan           Port 3306 reported      Completed
                          open; 999 ports         
                          filtered                

  Service detection       MySQL-associated        Completed
                          service identified;     
                          exact version unknown   

  OS detection            Windows 11 suggested,   Completed
                          but result marked       
                          unreliable              

  NSE script              No additional MySQL     Completed
                          information returned    

  ACK scan                Five ports reported     Completed
                          filtered                

  Report                  Findings and            Completed
                          limitations documented  
  -----------------------------------------------------------------------

## 11. Conclusion

This lab demonstrated how Nmap can be used for host discovery, TCP port
scanning, service detection, OS fingerprinting, NSE script execution,
and filtering analysis.

The target was reported as reachable. TCP port 3306 was reported open by
the initial TCP scan and associated with MySQL by service detection. OS
detection suggested Windows 11, but Nmap marked the result as
unreliable. The ACK scan reported all five tested ports as filtered.

These observations are not, by themselves, proof of a vulnerability.
Further authorized verification would be needed to confirm the identity
of the service on port 3306 and understand the network filtering
behavior.

## 12. Skills Practiced

-   Nmap host discovery
-   TCP port scanning
-   Service and version detection
-   OS fingerprinting
-   NSE script usage
-   TCP ACK scanning
-   Interpreting open and filtered ports
-   Cybersecurity lab documentation

## 13. Evidence Screenshots

Add your own terminal screenshots to a `screenshots/` folder. Redact IP
addresses and any other identifying information before publishing.

Suggested structure:

``` text
nmap-network-scanning/
├── README.md
└── screenshots/
    ├── 01-host-discovery.png
    ├── 02-port-scanning.png
    ├── 03-service-detection.png
    ├── 04-os-detection.png
    ├── 05-nse-scanning.png
    └── 06-filtering-analysis.png
```

Only include screenshots you actually captured. Do not fabricate scan
evidence.

## 14. Responsible Use

Run Nmap only against systems you own or have explicit permission to
assess. This report is based on a personal lab exercise.
