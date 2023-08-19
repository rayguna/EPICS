﻿# Exercise: Create an example IOC on EPICS

*Goal*: To create a simple Input/Output Controller (IOC) using EPICS.

**Basic Terminologies**

EPICS: EPICS stands for Experimental Physics and Industrial Control System. An EPICS control system can contain one or more IOCS and Client Workstations (CWS) that can communicate over a local area network (LAN). Hence, it enables the creation of servers and client applications.

Channel Access: Arbitrary number of clients can access both CWs and IOCs, and a client can communicate with an arbitrary number of servers.

Process Variables (PV): EPICS process variables exist as objects abbreviated as PV. A PV object has both methods and attributes to access its properties. Clients communicate with the control system's IOC using the PV objects to monitor and control the various aspects of the system. 

Database: The EPICS process database is a collection of different types of records. Each record in the database represents a PV, which can be monitored, controlled, or manipulated. 

You can get the comprehensive list of the records using the command dbl. If you are overwhelmed with the result, you can use another command called dbgrep("<pattern>") to match a pattern. Here are some examples. Also see reference 5 below for more information.

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

##### Overview

1. Prepare the EPICS environment.
   
   1.1. Install EPICS base and test
   
   1.2. Create a dummy record
   
   1.3. Create an Ioc

2. Add the asyn package

3. Install StreamDevice

***

1. Prepare the EPICS environment
   
   1.1. Install EPICS base and test
   
   - Create EPICS directory and clone, download, and compile epics-base as follows. Note that the compilation process generally takes a while depending on your machine configurations. 
     
     ```
     mkdir EPICS
     cd EPICS
     git clone --recursive https://github.com/epics-base/epics-base.git
     cd epics-base
     make
     ```
   
   - Include the path into either .profile or .bashrc. In this case, I am including the path into bashrc.
   
   - Open the bashrc editor using the command:
     
     ```
     gedit ~/.bashrc
     ```
   
   - Remember to click on the Save button before quitting.
   
   - To execute the changes, type source ~/.bashrc.
   
   - To check if EPICS has been installed correctly, type softIoc. 
   
   - To check for records, type dbl. In this case, nothing is listed because no records have been created yet.
   
   - To exit, press ctrl + c.
   
   1.2. Create a dummy record
   
   - To make this exercise more interesting, let's create a dummy record called sensor. 
   
   - Create the record using a text editor and save it with extension .db. In this case, we save the file as sensor.db which has the following contents. 
     
     ```
     record(ai, "sensor:temperature")
     {
            field(DESC, "sensor temperature reading.")
            field(VAL, 25)
     }
     ```
   
   - First create the file using the command touch sensor.db.
   
   - Next, open the file using the command gedit sensor.db and write the following into the file. Remember to save before closing the text editor. Also give permission to read, write, and execute using the command chmod -rwx sensor.db.
   
   - Re-run EPICS while calling the newly created database using the command softIoc -d sensor.db.
   
   - Open a new terminal and type caget sensor:temperature to get the temperature value. Note that if you call the caget command on the epics terminal, you will receive an error message saying that Command caget not found. You can also update the value using the command caput sensor:temperature 28.
   
   - You can access the pv's attribute e.g., sensor:temperature.DESC or sensor.temperature.VAL.
   
   - You can finally verify the power of EPICS by calling accessing the newly created pv from a different computer that is connected to the same network. 
   
   - In my case, I access the pv from a windows machine with anaconda installed. I created an environment called minimal_py39 in which I have installed pyepics using the command pip install pyepics. To use python editor, type python. I then access the pv as follows. Quit the python editor by typing exit().
     
     ```
     from epics import caget
     caget('sensor:temperature.VAL')
     caget('sensor:temperature.DESC')
     ```
   
   1.3. Create an Ioc
   
   - You can create as many IOCs as you desire, but, for this example, we will create just one IOC.
   
   - Let's create a parent folder called IOCs and a child folder called testIoc and navigate into the child folder as follows.
     
     ```
     mkdir -p $HOME/EPICS/IOCs/testIoc
     cd IOCs/testIoc
     ```
   
   - Create the testIoc as follows.
     
     ```
     makeBaseApp.pl -t example testIoc
     makeBaseApp.pl -i -t example testIoc
     Application name? <just hit return>
     make
     ```
   
   - Navigate to iocBoot/ioctestIoc and type chmod u+x st.cmd.
   
   - Run st.cmd using the command ./st.cmd. This will start the testIoc.
   
   - Type dbl to list the ioc records.
   
   - You can verify that the ioc is running by applying the caget command to any of the listed records from another terminal.

2. Add the Asyn package
   
   - Go back to the EPICS directory, create a support directory, download and install the asyn package, and navigate into the newly created asyn folder as follows.
     
     ```
     cd $HOME/EPICS
     mkdir support
     cd support
     git clone https://github.com/epics-modules/asyn.git
     cd asyn
     ```
   
   - To be continued...
