# Reverse Shell Network Detection Study

## Overview
This project investigates suspicious outbound network traffic in a controlled lab environment using Wireshark. The main goal is to analyze packet captures that resemble reverse-shell-style communication and identify network-based indicators such as unusual outbound connections, suspicious destination ports, interactive plaintext sessions, and periodic beacon-like traffic.

The project is focused on defensive cybersecurity learning and packet analysis. Rather than emphasizing exploitation, the lab traffic was intentionally generated to simulate suspicious callback behavior for safe capture and analysis.

## Objective
The objective of this project is to understand how suspicious post-compromise communication can appear at the network level and how those patterns can be identified through packet analysis.

This project focuses on:
- capturing normal baseline traffic
- identifying outbound connections to uncommon ports
- analyzing interactive bidirectional TCP sessions
- detecting repeated beacon-like connections
- documenting findings using Wireshark

## Lab Environment
The lab was built using two virtual machines on the same isolated network:

- **Ubuntu Victim:** `192.168.28.128`
- **Kali Linux Analyzer / Listener:** `192.168.28.129`

This setup allowed traffic generation and packet inspection in a safe lab environment.

## Tools Used
- **Kali Linux** — used as the analysis and listener system
- **Ubuntu** — used as the victim machine
- **Netcat** — used to generate controlled TCP communication for analysis
- **Wireshark** — used to capture and inspect network traffic
- **Python 3** — used to run a simple local HTTP server during baseline testing

## Project Scenarios

### 1. Baseline Traffic Analysis
A baseline packet capture was collected first to observe normal communication between the two hosts. This included ICMP traffic and local HTTP traffic. The purpose of this step was to establish a normal traffic reference before generating suspicious activity.

### 2. Interactive Outbound TCP Session
A second capture focused on an outbound TCP connection from the Ubuntu victim to Kali on port `4444`. This session contained bidirectional plaintext traffic that resembled an interactive command-and-response pattern. The traffic was reviewed to understand how suspicious outbound sessions can differ from normal behavior.

### 3. Beacon-Like Outbound Connections
A third capture focused on repeated short outbound connections from the victim to Kali on port `5555`. These repeated connections simulated beacon-like behavior and were analyzed for timing, repetition, and consistency of destination usage.

## Analysis Approach
The packet captures were analyzed in Wireshark using:
- packet filtering by port and protocol
- TCP conversation analysis
- Follow TCP Stream
- I/O Graph analysis
- comparison against baseline traffic

This approach made it possible to distinguish suspicious traffic from normal communication and identify useful detection indicators.

## Key Findings
The analysis showed that suspicious traffic could be clearly separated from baseline activity.

Main findings:
- outbound connections to uncommon ports appeared only during suspicious scenarios
- the TCP session on port `4444` showed readable bidirectional traffic resembling an interactive exchange
- traffic on port `5555` showed repeated short connections to the same destination
- the suspicious patterns were not present in the baseline capture

These observations demonstrate how packet analysis can help identify potentially malicious communication patterns even without endpoint telemetry.

## Detection Value
This project highlights several useful detection ideas for defenders:
- monitoring outbound traffic to uncommon ports
- baselining normal host communication
- identifying interactive plaintext TCP sessions
- detecting repeated connections to the same destination at regular intervals
- using timing and conversation statistics to support investigations

## Conclusion
This project demonstrates how Wireshark can be used to investigate suspicious outbound traffic patterns in a controlled lab. By comparing baseline traffic with interactive and beacon-like communication, it becomes easier to recognize network indicators associated with post-compromise behavior.
