Record \#509

# SMASForge Pro (v1.0.0)

Spacecraft Onboard Interface Services (SOIS) — Subnetwork Memory Access Service Studio (CCSDS 852.0-M-1)  
MAGENTA BOOK COMPLIANT

Memory Endpoints

4 Nodes

DMA Access Latency

45 μs

Transactions Total

0

Normative Audit

100%

## 

Memory Transaction Controller

---

Destination Memory Node 0x20 \- Mass Memory Unit (SpaceWire RMAP) 0x32 \- ADCS Computer (MIL-1553B DMA) 0x48 \- Power & Thermal (CANopen SDO) 0x99 \- Offline Payload (Fault Target) Memory Offset Address (Hex) Transfer Length (Bytes) Write Payload (Hex String) Execute READ.request Execute WRITE.request (Verified) Execute CHECK.request (CRC-16) Run CCSDS 852.0 Normative Audit

## 

Subnetwork Protocol Frame

---

Protocol: SpaceWire RMAP  
Header CRC: 0x00  
Data CRC: N/A

Wire Hex: 20014C20FE...

## 

Memory Hex Inspection Dump

---

00001000: 00 01 02 03 04 05 06 07 08 09 0A 0B 0C 0D 0E 0F \|................\| 00001010: 10 11 12 13 14 15 16 17 18 19 1A 1B 1C 1D 1E 1F \|................\| 00001020: 20 21 22 23 24 25 26 27 28 29 2A 2B 2C 2D 2E 2F \| \!"\#\$%&'()\*+,-./\| 00001030: 30 31 32 33 34 35 36 37 38 39 3A 3B 3C 3D 3E 3F \|0123456789:;\<=\>?\|

## 

SOIS Primitives Execution Console

---

\[INIT\] SMASForge Pro v1.0.0 Ready. Standard: CCSDS 852.0-M-1. \[SOIS\] Registered 4 Spacecraft Memory Endpoints. \[STATUS\] Ready to dispatch READ.request, WRITE.request, and CHECK.request primitives.

## 

Normative Compliance Audit Matrix (CCSDS 852.0-M-1)

---

| Req ID | Clause | Requirement Name | Evidence / Verified Rule | Verdict   |
| :---- | :---- | :---- | :---- | :---- |
| SMAS-001 | §3.1.2 | Memory Space Abstraction | Destination node address & offset bounds | PASS |
| SMAS-002 | §3.2.2 | READ.request Primitive | Dispatches target, offset, length parameters | PASS |
| SMAS-003 | §3.2.3 | READ.indication Delivery | Delivers verified data octets & status | PASS |
| SMAS-004 | §3.2.4 | READ\_FAILURE.indication | Bounds overflow & unreachable node fault | PASS |
| SMAS-005 | §3.2.5 | WRITE.request with Verify | Verified write commit & read-back ack | PASS |

