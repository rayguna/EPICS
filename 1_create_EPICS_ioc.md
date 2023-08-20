### Exercise: Create an example IOC on EPICS

*Goal*: To create a simple Input/Output Controller (IOC) using EPICS.

##### Overview

1. Prepare the EPICS environment
   
   1.1. Install EPICS base and test
   
   1.2. Create a dummy record
   
   1.3. Create an Ioc

2. Add the asyn package

3. Install StreamDevice

4. Modify our Ioc to include the asyn package and StreamDevice

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
   
   - Navigate to `$HOME/EPICS/support/asyn/configure` and type gedit RELEASE.
   
   - Replace the contents of the file with the following.
     
     ```
     #RELEASE Location of external products
     HOME=/home/your_username
     SUPPORT=$(HOME)/EPICS/support
     -include $(TOP)/../configure/SUPPORT.$(EPICS_HOST_ARCH)
     # IPAC is only necessary if support for Greensprings IP488 is required
     # IPAC release V2-7 or later is required.
     #IPAC=$(SUPPORT)/ipac-2-14
     # SEQ is required for testIPServer
     #SNCSEQ=$(SUPPORT)/seq-2-2-5
     # EPICS_BASE 3.14.6 or later is required
     EPICS_BASE=$(HOME)/EPICS/epics-base
     -include $(TOP)/../configure/EPICS_BASE.$(EPICS_HOST_ARCH)
     ```
   
   - Note to change your_username to yours. Also note that IPAC=... and SNCSEQ=... are commented.
   
   - Next, while still in the same directory (i.e., ../asyn/configure), type make. Make sure that no error messages are generated. If so, note the error and fix the contents of the RELEASE file accordingly.

3. Install StreamDevice
   
   - Go back to the EPICS/support directory, download and install StreamDevice, navigate into the newly created package, and remove GNUmakefile as follows.
     
     ```
     cd $HOME/EPICS/support
     git clone https://github.com/paulscherrerinstitute/StreamDevice.git
     cd StreamDevice/
     rm GNUmakefile
     ```
   
   - Navigate to `$HOME/EPICS/support/StreamDevice/configure` and type gedit RELEASE.
   
   - Modify the contents of the file as follows:
     
     - Add HOME=/home/your_username
     
     - Set EPICS_BASE as: `EPICS_BASE=$(HOME)/EPICS/epics-base`
     
     - Place the above codes above TEMPLATE_TOP in which EPICS_BASE is stated.
     
     - Set ASYN as: `ASYN=$(SUPPORT)/asyn`
     
     - Comment out CALC=... and PRCE=... CALC requires you to install the calc module from SynAps first, which is skipped in this exercise.  
     
     - PCRE is used for regular expression matching for which you need to define the header file and library as follows.
       
       ```
       PRCE_INCLUDE=/usr/include/pcre
       PCRE_LIB=/usr/lib
       ```
     
     - For 64 bit, define it as follows. You can navigate into the respective paths to verify their existence.
       
       ```
       PCRE_INCLUDE=/usr/include/pcre
       PCRE_LIB=/usr/lib64
       ```
     
     - Finally, while still in the same directory (i.e., ../StreamDevice/configure), type make. Make sure that no error messages are generated. If so, note the error and fix the contents of the RELEASE file accordingly. 

4. Modify our Ioc to include the Asyn package and StreamDevice [8]
   
   - **Stop!** This section is a work in progress. I have not been able to include the Asyn and StreamDevice packages to generate a working Ioc.
   
   - I encountered Error 2 and unable to identify the issue while making modifications to the various files, so I decide to delete the testIoc folder and restart from scratch.
   
   - Follow the instructions in section 1.3 to re-make the testIoc. In section 1.3, we were able to create a functional Ioc that contains the example records which can be called with the command `dbl`. Here, we will further incorporate the Asyn and StreamDevice packages to the Ioc.   
   
   - Re-make testIoc:
     
     - To re-make testIoc, first delete the entire testIoc folder while in the parent directory, `$HOME/EPICS/IOCs`, using the command rm -R testIoc. Then, type:
       
       ```
       mkdir testIoc
       cd testIoc
       makeBaseApp.pl -t example testIoc
       makeBaseApp.pl -i -t example testIoc
       Application name? <just hit return>
       make
       ```
   
   - To link testIoc to Asyn and StreamDevice, it must load asyn.dbd and stream.dbd:
     
     - On the terminal, type `cd testIocApp/src` to navigate to `$HOME/EPICS/IOCs/testIoc/testIocApp/src/`
     
     - Type `gedit Makefile` and below the section `Link in the code from our support library` add the following. Save the file before closing.
       
       ```
       #add asyn and StreamDevice to this IOC production libs
       testIoc_LIBS += stream
       testIoc_LIBS += asyn
       ```
   
   - Next, open another file within the same directory called xxxSupport.dbd using the command `gedit xxxSupport`. This file is referred to in the Makefile. Include stream.dbd and asyn.dbd. The xxxSupport.dbd file should look like the following. Save the file before closing.
     
     ```
     include "xxxRecord.dbd"
     device(xxx,CONSTANT,devXxxSoft,"SoftChannel")
     
     #
     include "stream.dbd"
     include "asyn.dbd"
     registrar(drvAsynIPPortRegisterCommands)
     registrar(drvAsynSerialPortRegisterCommands)
     registrar(vxi11RegisterCommands)
     ```
   
   - Next, you need to specify the paths to ASYN and STREAM in the testIoc/configure/RELEASE file under the section called `variables and paths to dependent modules` as follows. Remember to include the path definition to HOME.
     
     ```
     # Variables and paths to dependent modules:
     HOME=/home/your_username
     SUPPORT = ${HOME}/EPICS/support
     ASYN=$(SUPPORT)/asyn
     STREAM=$(SUPPORT)/stream
     ```
   
   - Next, navigate to `IOCs/testIoc`. 
   
   - If you have previously run the make command, you must first call the `make distclean`, followed by the `make` command.
   
   - Note that if you run the make distclean command in the IOCs/testIoc/configure folder, you will receive an error message, `make: *** No rule to make target 'distclean'. Stop`. Make sure to address any error messages before proceeding.
   
   - One common error is caused by some of the files being write protected. To overcome this error, go up to the parent directory (e.g., `$HOME` or the home directory) and type `chmod -R 777` EPICS.
   
   - Finally, type `make`. Likewise, make sure to address any error messages before proceeding.
   
   - In my case, I get Error 255 and Error 2 since it can't find stream.dbd while reading xxxSupport.dbd. 
   
   - Commenting out the above addition to the Makefile, xxxSupport, and RELEASE files and running the make distclean followed by the make commands, however, was successful.
   
   - To be continued... 
