# SMASForge Pro (v1.0.0)

## CCSDS 852.0-M-1 Subnetwork Memory Access Service (SMAS) Reference Implementation & Avionics Memory Workstation

[![Standard](https://img.shields.io/badge/CCSDS-852.0--M--1-blue.svg)](https://ccsds.org/Pubs/852x0m1.pdf) [![SOIS Area](https://img.shields.io/badge/SOIS-Subnetwork_Memory_Access-00b0ff.svg)](https://sanaregistry.org/) ![Compliance](https://img.shields.io/badge/Compliance-10%2F10_PASS-brightgreen.svg) ![License](https://img.shields.io/badge/License-Commercial_Flight_Lab_%2F_Dual_MIT-indigo.svg)

SMASForge Pro is a production-grade implementation, simulation engine, and verification harness for the **Consultative Committee for Space Data Systems (CCSDS) 852.0-M-1** standard: *"Spacecraft Onboard Interface Services (SOIS) — Subnetwork Memory Access Service"*.

It provides satellite flight software engineers, onboard avionics test bench teams, and spacecraft AIT (Assembly, Integration, and Test) specialists with a unified framework to perform random-access memory reads, writes, and block integrity checks across SpaceWire (ECSS-E-ST-50-52C RMAP), MIL-STD-1553B DMA, and CANopen SDO memory domains.

---

## 1\. Key Capabilities

- **Full Normative Primitives Support (§3.2)**:  
  - `READ.request` / `READ.indication`: Arbitrary length octet reads with microsecond latency profiling.  
  - `WRITE.request` / `WRITE.indication`: Parameterized write transactions with optional read-back verification acknowledgment.  
  - `CHECK.request` / `CHECK.indication`: High-speed hardware-level CRC-16 memory block integrity verification without raw payload bus transport.  
  - `READ_MODIFY_WRITE.request`: Atomic bitmask mutation preventing multi-thread race conditions on registers.  
  - Comprehensive Failure Reporting: `DESTINATION_UNREACHABLE`, `INVALID_MEMORY_ADDRESS`, `PROTECTION_VIOLATION`, `TRANSACTION_TIMEOUT`.  
- **Subnetwork Convergence Encoders**:  
  - SpaceWire RMAP (ECSS-E-ST-50-52C): Protocol ID `0x01`, Header CRC-8 (\$x^8 \+ x^2 \+ x^1 \+ 1\$), Data CRC-8, and Logical Addressing.  
  - MIL-STD-1553B DMA: Subaddress block transfers up to 32 words per frame.  
  - CANopen SDO: Expedited and segmented memory domain transfers.  
- **Glass-Cockpit Diagnostics Studio**: Interactive web application featuring real-time aligned 16-octet memory hex dump inspector, RMAP packet dissector, and live compliance audit reporting.  
- **Headless Automation CLI**: Python 3 command-line utility for CI/CD test bench integration and automated spacecraft qualification loops.

---

## 2\. Directory Structure

smas-forge-pro/

├── source-code/

│   ├── smas-engine.js           \# Core ES6 SOIS SMAS service layer & CRC engines

│   ├── smas-auditor.js          \# 10-point normative compliance audit engine

│   ├── smas\_cli.py              \# Python 3 CLI suite for avionics test benches

│   ├── test\_runner.py          \# Automated verification test suite (10/10 PASS)

│   └── index.html              \# Glass-cockpit web diagnostics studio

├── documentation/

│   ├── CCSDS\_852.0\_SMAS\_SPECIFICATION.md  \# Detailed technical specification

│   └── SMAS\_COMPLIANCE\_MATRIX.md         \# 10-point clause verification matrix

├── screenshots/

│   ├── smas-architecture-diagram.svg     \# Full-stack vector architecture diagram

│   └── smas-studio-preview.svg            \# Diagnostics workstation interface mockup

├── build/

│   └── smas-forge-pro-v1.0.0.zip          \# Production distribution package

└── README.md                            \# Comprehensive product manual

---

## 3\. Quickstart Guide

### 3.1 Running the Automated Test Suite

Verify that all 10 unit test cases pass:

python3 source-code/test\_runner.py

Expected output:

\[PASS\] TEST-SMAS-001: Destination Subnetwork & Memory Address Space Abstraction (§3.1.2)

\[PASS\] TEST-SMAS-002: READ.request Service Primitive Execution (§3.2.2)

\[PASS\] TEST-SMAS-003: READ.indication Delivery & Payload Integrity (§3.2.3)

\[PASS\] TEST-SMAS-004: READ\_FAILURE.indication on Fault / Out of Bounds (§3.2.4)

\[PASS\] TEST-SMAS-005: WRITE.request Service Primitive Execution (§3.2.5)

\[PASS\] TEST-SMAS-006: WRITE.indication Delivery & Memory Verification (§3.2.6)

\[PASS\] TEST-SMAS-007: WRITE\_FAILURE.indication on Memory Protection Violation (§3.2.7)

\[PASS\] TEST-SMAS-008: CHECK.request Primitive & CRC-16 Memory Integrity Validation (§3.2.8)

\[PASS\] TEST-SMAS-009: READ\_MODIFY\_WRITE Primitive & Atomic Bitmask Modification (§3.2.11)

\[PASS\] TEST-SMAS-010: SpaceWire RMAP Protocol ID (0x01) & CRC-8 Wire Framing (§2.3 & ECSS)

\------------------------------------------------------------

10/10 TESTS PASSED (100% SUCCESS RATE)

### 3.2 Running the Avionics Test Bench CLI

List registered subnetwork memory endpoints:

python3 source-code/smas\_cli.py list-nodes

Read 16 octets from Mass Memory Unit (`0x20`) at offset `0x1000`:

python3 source-code/smas\_cli.py read-mem \--dest 0x20 \--addr 0x1000 \--len 16

Write verified payload to offset `0x2000`:

python3 source-code/smas\_cli.py write-mem \--dest 0x20 \--addr 0x2000 \--hex DEADBEEFCAFEBABE \--verify

Verify memory block integrity using hardware CRC-16:

python3 source-code/smas\_cli.py check-mem \--dest 0x20 \--addr 0x1000 \--len 16

Dump formatted memory region:

python3 source-code/smas\_cli.py dump-mem \--dest 0x20 \--addr 0x1000 \--len 64

Execute full CCSDS 852.0-M-1 normative audit:

python3 source-code/smas\_cli.py audit

### 3.3 Launching the Glass-Cockpit Web Studio

Open `source-code/index.html` in any modern web browser to access the interactive memory workstation.

---

## 4\. Technical Architecture & Primitives Reference

\+--------------------------------------------------------------+

\|                     Flight Software Entity                   \|

\+--------------------------------------------------------------+

         \| READ/WRITE/CHECK.request        ^ READ/WRITE/CHECK.indication

         v                                 \| FAILURE.indications

\+--------------------------------------------------------------+

\|          SOIS Subnetwork Memory Access Service (SMAS)        \|

\|  \- Address Space Mapper & Protection Guard (Boot ROM)        \|

\|  \- CRC Engine (CRC-8 RMAP Poly 0x07, CRC-16 Poly 0x1021)    \|

\|  \- Atomic Read-Modify-Write (RMW) Engine                     \|

\+--------------------------------------------------------------+

         \|                                 ^

         v                                 \|

\+--------------------------------------------------------------+

\|         Subnetwork Specific Convergence Layer (SSCL)         \|

\|  \- SpaceWire RMAP (ECSS-E-ST-50-52C Protocol ID 0x01)        \|

\|  \- MIL-STD-1553B DMA Subaddress Block Transfers              \|

\|  \- CANopen SDO Memory Domain Expedited/Segmented Transfers   \|

\+--------------------------------------------------------------+

---

## 5\. Monetization & Licensing

- **Commercial Constellation License**: \$22,500 / year / constellation including flight-qualified C/C++ SOIS SMAS flight library, NASA cFS application wrappers, and Yamcs ground station adapters.  
- **Flight Qualification Audits**: \$3,500 / day on-site / remote test bench qualification.  
- **Dual MIT / Educational License**: Open-core simulator available for university CubeSat teams and non-commercial aerospace research.

---

## 6\. Authoritative Standards & References

- **CCSDS 852.0-M-1**: [Spacecraft Onboard Interface Services — Subnetwork Memory Access Service](https://ccsds.org/Pubs/852x0m1.pdf) (Magenta Book, December 2009).  
- **CCSDS 850.0-G-1**: Spacecraft Onboard Interface Services — An Overview (Green Book).  
- **ECSS-E-ST-50-52C**: SpaceWire — Remote Memory Access Protocol (RMAP).  
- **CiA 301**: CANopen Application Layer and Communication Profile.