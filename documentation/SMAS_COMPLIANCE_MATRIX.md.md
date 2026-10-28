# CCSDS 852.0-M-1 Normative Compliance Matrix

## Standard: Spacecraft Onboard Interface Services — Subnetwork Memory Access Service

**Document ID**: CCSDS 852.0-M-1 (Magenta Book, Issue 1, December 2009\)  
**Verification Date**: 2026-10-20  
**Overall Verdict**: **10 / 10 PASS (100.0% COMPLIANT)**  
**Certification Status**: `CCSDS_852_0_SMAS_CERTIFIED`

| Requirement ID | Specification Clause | Requirement Summary | Verification Method & Implementation | Result |
| :---- | :---- | :---- | :---- | :---- |
| **SMAS-001** | §3.1.2 | Address Space Abstraction | Abstract Destination Address and Memory Address parameters identify nodes and memory offsets across heterogeneous subnets. | **PASS** |
| **SMAS-002** | §3.2.2 | `READ.request` Primitive | Validated parameter signature: `Destination_Address`, `Memory_Address`, `Length`. Correctly dispatches read requests. | **PASS** |
| **SMAS-003** | §3.2.3 | `READ.indication` Delivery | Delivers requested memory octets and completion status upon successful subnetwork read. | **PASS** |
| **SMAS-004** | §3.2.4 | `READ_FAILURE.indication` | Generates explicit failure indication with reason codes when out of bounds or target is offline. | **PASS** |
| **SMAS-005** | §3.2.5 | `WRITE.request` Execution | Dispatches write request with Destination Address, Memory Address, Data, and optional Verification Ack flag. | **PASS** |
| **SMAS-006** | §3.2.6 | `WRITE.indication` Delivery | Confirms write transaction and verifies read-back data matching payload. | **PASS** |
| **SMAS-007** | §3.2.7 | Protection Violation Trapping | Enforces read-only memory bounds (e.g., Boot ROM) and dispatches `PROTECTION_VIOLATION` failure indication. | **PASS** |
| **SMAS-008** | §3.2.8 \- §3.2.10 | `CHECK.request` Integrity | Calculates subnetwork-level CRC-16 checksums across memory blocks and detects discrepancies. | **PASS** |
| **SMAS-009** | §3.2.11 | `READ_MODIFY_WRITE` Primitive | Executes atomic bitmask update transaction `(old & ~mask) | (new & mask)` without race conditions. | **PASS** |
| **SMAS-010** | §2.3 & ECSS | SpaceWire RMAP Convergence | Encapsulates packets with SpaceWire RMAP Protocol ID `0x01`, Header CRC-8, Data CRC-8, and Logical Addressing. | **PASS** |

---

## Detailed Test Verification Summary

All 10 test cases in `test_runner.py` and `smas-auditor.js` executed synchronously with 100% pass rate. Zero regressions detected across SpaceWire RMAP, MIL-STD-1553B DMA, and CANopen SDO transceivers.