### EPICS-related Projects

**Contents**

1. create_EPICS_ioc.md

***

**Basic Terminologies**

<u>EPICS</u>: EPICS stands for Experimental Physics and Industrial Control System. An EPICS control system can contain one or more IOCS and Client Workstations (CWS) that can communicate over a local area network (LAN). Hence, it enables the creation of servers and client applications.

<u>Channel Access</u>: Arbitrary number of clients can access both CWs and IOCs, and a client can communicate with an arbitrary number of servers.

<u>Process Variables (PV)</u>: EPICS process variables exist as objects abbreviated as PV. A PV object has both methods and attributes to access its properties. Clients communicate with the control system's IOC using the PV objects to monitor and control the various aspects of the system. 

<u>Database</u>: The EPICS process database is a collection of different types of records. Each record in the database represents a PV, which can be monitored, controlled, or manipulated. 

You can get the comprehensive list of the records using the command dbl. If you are overwhelmed with the result, you can use another command called dbgrep("<pattern>") to match a pattern. Here are some examples. Also see reference 5 below for more information.

<u>Asyn Package</u>: It is short for "asynchronous" and is a fundamental EPICS package that provides a framework for developing device support for various types of hardware interfaces. It creates asynchronous communication channels between EPICS applications and hardware devices that communicate over various protocols (e.g., serial, network, and USB). 

<u>StreamDevice package</u>: It is built upon the `asyn` package and provides higher-level abstractions for creating device support in EPICS. It is used for managing devices that generate or consume continuous streams of data (e.g., scientific cameras, detectors, and data acquisition systems).

```
dbgrep("*led*")
dbgrep("*led")
```

**References**:

1. [Creation of an Input/Output Controller (IOC) &mdash; EPICS How-Tos documentation](https://docs.epics-controls.org/projects/how-tos/en/latest/getting-started/creating-ioc.html)

2. [Getting Started - EPICS Controls](https://epics-controls.org/resources-and-support/documents/getting-started/)

3. [EPICS Process Database Concepts &mdash; EPICS Documentation documentation](https://docs.epics-controls.org/en/latest/guides/EPICS_Process_Database_Concepts.html#database-links-to-passive-record)

4. https://epics.anl.gov/EpicsDocumentation/AppDevManuals/AppDevGuide/3.12BookFiles/chapter2.html

5. https://epics.anl.gov/base/R3-14/12-docs/AppDevGuide/node10.html#SECTION001020000000000000000

6. [PyEpics Overview &#8212; Epics Channel Access for Python](https://pyepics.github.io/pyepics/overview.html#quick-start)

7. https://epics.anl.gov/base/R3-14/12-docs/AppDevGuide/node3.html#SECTION00360000000000000000

8. [Creation of an Input/Output Controller (IOC) &mdash; EPICS How-Tos documentation](https://docs.epics-controls.org/projects/how-tos/en/latest/getting-started/creating-ioc.html)

9. [EPICS serial communication with Arduino: creation of project, protocol file, database... · GitHub](https://gist.github.com/inigoalonso/99d9076c672661a4b821)

10. [Getting Started - EPICS Controls](https://epics-controls.org/resources-and-support/documents/getting-started/)

11. Volker Ziemann, "A Hands-On Course in Sensors Using the Arduino and Raspberry Pi (Series in Sensors)", CRC Press, 1st Ed., 2018.

***