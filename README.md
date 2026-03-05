# Experimental EtherNet/IP Scanner Stack v(0.5.0)

This project provides an experimental EtherNet/IP scanner implementation designed to turn a Python-hosted application into a functional EtherNet/IP I/O device. It is intended for student use, academic work, laboratory environments, and internal research and development (R&D).

The scanner runs on Windows and Linux using Python 3.9 and newer. It is distributed as a compiled binary module that is imported and controlled from Python, allowing application code to focus on device behavior and data handling rather than protocol framing and transport details.

⚠️ This software is experimental and NOT ODVA CT-certified. It must not be used in safety-critical, certified, or production industrial systems.

---

## Overview

EtherNet/IP adapts the Common Industrial Protocol (CIP) over standard Ethernet to allow PLCs and field devices to exchange real-time I/O and configuration data using implicit and explicit messaging.

This scanner implementation exposes standard CIP objects over UDP for Class 1 I/O connections and TCP for Class 3 explicit messaging. A Python application interacts with the scanner through callbacks, while the compiled binary module handles the EtherNet/IP protocol stack internally.

The current release targets Linux and Windows platforms. LLDP object behavior is available on Linux hosts only.

---

## Platform Support

- Operating Systems: Windows, Linux
- Python: 3.9 and newer
- Architecture: Python application + compiled binary extension module
- Network Interfaces: Standard Ethernet

---

## Capabilities

### Object Set
Implements the following CIP objects:
- Identity (0x01)
- Connection Manager (0x06)
- TCP/IP Interface (0xF5)
- Ethernet Link (0xF6)
- Port Object (0xF4)
- QoS (0x48)
- LLDP Management and Agent (0x109 / 0x10A, Linux-only)

### Messaging Modes
Supports both Class 1 implicit I/O and Class 3 explicit messaging, including connected and unconnected messaging, cyclic and change-of-state connections, and both standard and large Forward Open operations.

### Run/Idle Header
Full support for the 32-bit Run/Idle header in both O→T and T→O directions, including the additional 4-byte Run/Idle segment within the I/O payload.

### Unicast and Multicast
T→O producer connections support both unicast and multicast data distribution to consuming scanners.

### Timing and Robustness
Validated with Requested Packet Intervals (RPIs) down to 3 ms. Encapsulation timeout behavior has been exercised at 120 seconds, and UCMM flooding tests have been performed for durations up to 10 seconds to evaluate stability.

### Multi-Adapter Connection
Multiple logical adapters can be hosted within a single process by assigning distinct IP.

### Python-First Delivery
The stack is delivered as a compiled Python extension module and is consumed from Python via callbacks for I/O data exchange, configuration services, TCP/IP and Ethernet handling, LLDP, QoS management, and reset behavior.

### Conformance Status
The implementation is designed with ODVA recommendations in mind but has not undergone CT conformance testing. It is suitable for early adopters, laboratory use, academic projects, and environments where formal ODVA certification is not a strict requirement.

---

## Intended Use

This software is intended for:
- Student and educational projects
- Academic research
- Laboratory testing
- Internal R&D and prototyping
- EtherNet/IP experimentation and learning

It is NOT intended for:
- Certified EtherNet/IP devices
- Safety-critical systems
- Commercial production deployment
- Systems requiring formal protocol conformance claims

---

## Licensing

This project is distributed under an Experimental Binary-Only License. Please see the [LICENSE](https://github.com/nucleonautomation/EnIP-Scanner/blob/main/LICENSE.md)  file for full terms and conditions.

EtherNet/IP is a trademark of ODVA. This project is not affiliated with, endorsed by, or approved by ODVA.

---

## Disclaimer

This software is provided "AS IS" and without warranty of any kind. Use of this software is entirely at your own risk.

---

## Change Log

- See the [LOG](https://github.com/nucleonautomation/EnIP-Scanner/blob/main/LOG.md) file for details.

---

## Quick Start

- See the [QUICK START](https://github.com/nucleonautomation/EnIP-Scanner/blob/main/QUICKSTART.md) file for details.

---

## Manual

- See the [MANUAL](https://github.com/nucleonautomation/EnIP-Scanner/blob/main/MANUAL.md) file for details.

---