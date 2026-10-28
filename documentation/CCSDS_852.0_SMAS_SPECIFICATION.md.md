# CCSDS 852.0-M-1 Subnetwork Memory Access Service (SMAS) Engineering Specification

## 1\. Executive Summary & Purpose

The **Subnetwork Memory Access Service (SMAS)** defined in **CCSDS 852.0-M-1 (Magenta Book, Issue 1, December 2009\)** provides a uniform, hardware-independent service interface allowing spacecraft onboard software entities (such as flight software applications, Device Access Service, or test benches) to access, read, write, and verify memory-mapped spaces across diverse spacecraft subnetworks.

SMAS abstracts underlying physical buses—including SpaceWire (ECSS-E-ST-50-52C RMAP), MIL-STD-1553B Direct Memory Access (DMA), and CANopen Service Data Object (SDO) domain transfers—into standardized random-access memory address spaces.

---

## 2\. Architectural Context in SOIS

Within the **CCSDS Spacecraft Onboard Interface Services (SOIS)** reference architecture (CCSDS 850.0-G-1):

- **Layer**: SOIS Subnetwork Layer.  
- **Service Classification**: Memory & Hardware Access Service.  
- **Sister Subnetwork Services**:  
  - Subnetwork Packet Service (SPS — CCSDS 851.0-M-1)  
  - Subnetwork Synchronisation Service (SSS — CCSDS 853.0-M-1)  
  - Subnetwork Device Discovery Service (SDDS — CCSDS 854.0-M-1)  
  - Subnetwork Test Service (STS — CCSDS 855.0-M-1)  
- **Upper Layer Consumers**:  
  - Device Access Service (DAS — CCSDS 871.0-M-1)  
  - Device Data Pooling Service (DDPS — CCSDS 873.0-M-1)  
  - Flight Software Memory Managers, Bootloaders, and FDIR Supervisors.

\+-------------------------------------------------------------+

\|              Flight Software / SOIS Application Layer       \|

\+-------------------------------------------------------------+

\|        SOIS Subnetwork Memory Access Service (SMAS)         \|

\|  \[READ.request\]   \[WRITE.request\]   \[CHECK.request\]  \[RMW\]  \|

\|  \[READ.ind\]       \[WRITE.ind\]       \[CHECK.ind\]             \|

\+-------------------------------------------------------------+

\|         Subnetwork Specific Convergence Layer (SSCL)        \|

\+--------------------+-------------------+--------------------+

\| SpaceWire RMAP     \| MIL-STD-1553B DMA \| CANopen SDO Domain \|

\| (Protocol ID 0x01) \| Subaddress Block  \| Expedited/Segment  \|

\+--------------------+-------------------+--------------------+

---

## 3\. Normative Service Primitives & Parameters (§3.2)

### 3.1 READ Primitives (§3.2.2 \- §3.2.4)

- **`READ.request (Destination Subnetwork Address, Memory Address, Length)`**: Initiates a non-intrusive memory block read from the target node starting at the specified memory address offset.  
- **`READ.indication (Destination Subnetwork Address, Memory Address, Length, Data, Status)`**: Delivers the fetched memory octets and completion status upon successful subnetwork read.  
- **`READ_FAILURE.indication (Destination Subnetwork Address, Memory Address, Length, Failure Reason)`**: Delivered when the memory read cannot be fulfilled due to address boundary violations or node unavailability.

### 3.2 WRITE Primitives (§3.2.5 \- §3.2.7)

- **`WRITE.request (Destination Subnetwork Address, Memory Address, Length, Data, Verification Flag)`**: Transmits data octets to be written to the target memory space. When the Verification Flag is asserted, mandatory read-back verification is executed.  
- **`WRITE.indication (Destination Subnetwork Address, Memory Address, Length, Status)`**: Confirms the successful writing and verification of the specified memory block.  
- **`WRITE_FAILURE.indication (Destination Subnetwork Address, Memory Address, Length, Failure Reason)`**: Reports write failure causes, such as write-protection violations in boot ROM segments, hardware locks, or verification mismatches.

### 3.3 CHECK Primitives (§3.2.8 \- §3.2.10)

- **`CHECK.request (Destination Subnetwork Address, Memory Address, Length, Expected Checksum)`**: Requests an autonomous hardware or subnetwork-level checksum calculation across a memory region without reading raw octets across the bus.  
- **`CHECK.indication (Destination Subnetwork Address, Memory Address, Length, Computed Checksum, Status)`**: Returns the computed CRC-16-CCITT checksum and comparison status.  
- **`CHECK_FAILURE.indication (Destination Subnetwork Address, Memory Address, Length, Failure Reason)`**: Generated if memory reading fails or checksum does not match expected reference.

### 3.4 READ\_MODIFY\_WRITE Primitive (§3.2.11)

- **`READ_MODIFY_WRITE.request (Destination Subnetwork Address, Memory Address, Mask, Data)`**: Executes an atomic bitwise read-modify-write operation at subnetwork level: \$\$\\text{NewValue} \= (\\text{OldValue} \\land \\neg\\text{Mask}) \\lor (\\text{Data} \\land \\text{Mask})\$\$

---

## 4\. Subnetwork Convergence Mappings (§2.3)

### 4.1 SpaceWire Remote Memory Access Protocol (RMAP — ECSS-E-ST-50-52C)

- **Protocol Identifier**: `0x01` (RMAP).  
- **Instruction Field**:  
  - Incremental / Non-incremental Write (`0x6C` / `0x68`)  
  - Incremental / Non-incremental Read (`0x4C` / `0x48`)  
  - Read-Modify-Write (`0x5C`)  
- **CRC Protection**:  
  - Header CRC-8 (\$x^8 \+ x^2 \+ x^1 \+ 1\$) covering target logical address through address and length fields.  
  - Data CRC-8 covering all payload octets.

### 4.2 MIL-STD-1553B DMA

- Maps memory addresses to assigned Remote Terminal (RT) subaddresses with block transfer counts (up to 32 data words per subaddress command).

### 4.3 CANopen SDO

- Maps 32-bit memory addresses to CANopen Object Dictionary index (16-bit) and sub-index (8-bit) using expedited (1–4 bytes) or segmented domain transfers (CiA 301).