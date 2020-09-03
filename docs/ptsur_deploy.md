Point Sur## Deployment - *R/V Point Sur* 2020

The information here covers the setup and installation information for the CORIOLIX system installed on the RCRV server Laconia which was shipped to USM on August 24 (arriving August 27), 2020.

### 1 Background

#### Physical Equipment Information
* __*Server Make/Model:*__ Dell PowerEdge R440 1U Server
* __*Server Name:*__ Laconia
* __*Server OS:*__ CentOS8

#### Account Information
* __*Local User Account:*__ rcrv
* __*Local User Account Password:*__ Please contact Chris or Jasmine

#### Network Information
Three Virtual Machines are run on the laconia server  
1. laconia-agg-ship - reads data from the network and writes data to local database  
2. laconia-pub-ship - holds a copy of the data and services the shipboard web user interface  
3. laconia-map-ship - a local mapserver to run the mapping/charting tools

Each machine is presented to the Point Sur shipboard wired network via a bridge on laconia’s eno1 interface.  Therefore, all 4 systems share one wired physical connection (only one network drop is required).  

__*PHYSICAL SERVER (laconia):*__ 192.168.1.164  
__*VM 1 (ptsur-agg-ship):*__ 192.168.1.161  
__*VM 2 (ptsur-pub-ship):*__ 192.168.1.162  
__*VM 3 (ptsur-maps-ship):*__ 192.168.1.163

### 2 Installation Procedures
**Overview:**  The following procedure covers physical installation and cabling of the CORIOLIX server: laconia on R/V Point Sur.

#### Install Host: laconia
1. Install laconia in rack location of your choice.
2. Connect power to both power supplies on the rear of the server.
3. Connect a network drop from the 192.168.1.0/24 network to Gb1 on the laconia.
4. Optionally, connect a keyboard, mouse, and display

### 3 Startup & Shutdown Procedures
**Overview:**
The following procedure covers starting the CORIOLIX system.  Please note that the host server laconia is setup such that the CORIOLIX Virtual Machines start automatically.  The CORIOLIX services installed on each Virtual Machine are also set to start automatically.  This means that you only need to work though the first section "Startup Host: Laconia" and may safely skip the subsequent startup steps.

Nonetheless, we'll include complete manual startup procedures here to provide you the options and instructions for starting CORIOLIX VMs or CORIOLIX services manually.  Instructions for manual startup should be performed in sequence as presented.

For shutdown we currently advise that you follow the manual shutdown instructions under sections "Shutdown CORIOLIX services", "Shutdown VM's: 1, 2, & 3", and "Shutdown Host".  The virtualized system has proven to be robust to controlled shutdowns and restarts of laconia (where the hypervisor manages the VM shutdowns) - but, we have additional testing to perform before we can be assured that this type of shutdown won't cause issue.

#### Startup Host: Laconia
1. Press power button on front to start server, allows machine to boot.  
2. At CentOS login use the rcrv account  
3. Right click on the network connections icon in the upper right of the desktop.  
4. Set the eno1 interface to be DHCP and check that the Point Sur DHCP server assigns the local **192.168.1.164** address to laconia.  
5.	Test DNS by pinging google (**ping 8.8.8.8**) or something like that – server needs to see the outside world.  
6.	If server gets expected IP (**192.168.1.164**) and can ping off-ship then we are good to start VM’s  

__*Note:*__ After completing the setup above and finishing with laconia on the Point Sur network at 192.168.1.164 you no longer need to keep a keyboard, mouse, and monitor plugged in to work with laconia.  You can disconnect those input and display devices.  The remaining setup steps may be performed through an ssh connection to laconia

#### Startup VMs: 1, 2, & 3

1. From your local machine on the 192.168.1.0/24 network open a terminal window or your ssh client
2. Make an SSH connection from your local terminal to laconia:

        >> ssh rcrv@192.168.1.164
        >> password: (enter user rcrv password)

3. Change to the root user:

        >> sudo su - root
        >> password: (enter user rcrv password)

4. List available virtual machines:

        >> virsh list --all

5. Expected List Results (id may vary):

        Id    Name                           State
        ----------------------------------------------------
        1     ptsur-agg-ship              stopped
        2     ptsur-maps-ship             stopped
        4     ptsur-pub-ship              stopped

6. If the VM's show that they are in a "running" State, then skip to step 9.

7. Start each VM from the command line (*Note*: VM startups usually take less about 60-90 seconds each.):

        >> sudo virsh start ptsur-agg-ship
        >> sudo virsh start ptsur-pub-ship
        >> sudo virsh start ptsur-maps-ship

8. Expected Startup Results:

        Id    Name                           State
        ----------------------------------------------------
        1     ptsur-agg-ship              running
        2     ptsur-maps-ship             running
        4     ptsur-pub-ship              running

9. Exit your ssh connection to laconia

        >> exit

##### Startup CORIOLIX services

The table below lists the CORIOLIX services that are installed on the "agg" and "pub" VMs:

vm: ptsur-agg-ship | vm: ptsur-pub-ship
--- | ---
service: sym_service | service: sym_service
service: coriolix | service: coriolix
service: mqtt | *   

*There is no mqtt service on ptsur-pub-ship

These servicess are set to start up automatically, but may be manually started or stopped on each virtual machine using the below workflows.

To start CORIOLIX follow the workflow below (you might find it helpful to open two ssh connections, one for each vm).

1. Start SymmetricDS

    Connect to ptsur-pub-ship via ssh.

        >> ssh rcrv@192.168.1.162
        >> password: (use rcrv account password)

    Check to see if symmetricds is already running (output below shows sym_service is already running)

        >> sudo /opt/SymmetricDS/symmetric-server-3.9.14/bin/sym_service status
        >> Installed: true
        >> Running: true
        >> Wrapper PID: 705
        >> Wrapper Running: true
        >> Werver Running: true

    If the sym_service isn't yet running, then:

        >> sudo /opt/SymmetricDS/symmetric-server-3.9.14/bin/sym_service start
        leave ssh session open (for step 2)

    Connect to ptsur-agg-ship via ssh.

        >> ssh rcrv@192.168.1.161
        >> password: (use rcrv account password)

    Check to see if symmetricds is already running (output below shows sym_service is already running)

        >> sudo /opt/SymmetricDS/symmetric-server-3.9.14/bin/sym_service status
        >> Installed: true
        >> Running: true
        >> Wrapper PID: 705
        >> Wrapper Running: true
        >> Werver Running: true

    If the sym_service isn't yet running, then:
        >> sudo /opt/SymmetricDS/symmetric-server-3.9.14/bin/sym_service start
        leave ssh session open (for step 2)

2. Start CORIOLIX Services

    From your open connection to ptsur-pub-ship, check to see if the CORIOLIX service is running:

        >> sudo systemctl status coriolix

    If not running, then:
        >> sudo systemctl start coriolix
        >> exit

    From your open connection to ptsur-agg-ship, check to see if the CORIOLIX service is running:

        >> sudo systemctl status coriolix
        >> sudo systemctl status mqtt

    If not running, then:

        >> sudo systemctl start coriolix; sudo systemctl start mqtt
        >> sudo systemctl status coriolix
        >> exit

##### Shutdown CORIOLIX services

Shutdown procedures are basically the startup procedures in reverse.

1. Shutdown CORIOLIX

    Connect to ptsur-agg-ship via ssh, leave ssh session open after running commands below.

        >> ssh rcrv@192.168.1.161
        >> password: (use rcrv account password)
        >> sudo sudo systemctl stop coriolix; sudo systemctl stop mqtt
        leave ssh session open (for step 2)

    Connect to ptsur-pub-ship via ssh, leave ssh session open after running commands below.

        >> ssh rcrv@192.168.1.162
        >> password: (use rcrv account password)
        >> sudo systemctl stop coriolix
        leave ssh session open (for step 2)

2. Stop SymmetricDS

    From your open connection to ptsur-agg-ship, execute:

        >> sudo /opt/SymmetricDS/symmetric-server-3.9.14/bin/sym_service stop
        >> exit

    From your open connection to ptsur-pub-ship, execute:

        >> sudo /opt/SymmetricDS/symmetric-server-3.9.14/bin/sym_service stop

##### Shutdown VM's: 1, 2, & 3

The VM shutdown process is also essentially the startup process in reverse.

1. From your local machine on the 192.168.1.0/24 network open a terminal window or your ssh client
2. Make an SSH connection from your local terminal to laconia:

        >> ssh rcrv@192.168.1.164
        >> password: (enter user rcrv password)

3. Change to the root user:

        >> sudo su - root
        >> password: (enter user rcrv password)

4. List available virtual machines:

        >> virsh list --all

5. Expected List Results (id may vary):

        Id    Name                           State
        ----------------------------------------------------
        1     ptsur-agg-ship              running
        2     ptsur-maps-ship             running
        4     ptsur-pub-ship              running

6. Start each VM from the command line (*Note*: VM startups usually take less about 60-90 seconds each.):

        >> sudo virsh stop ptsur-agg-ship
        >> sudo virsh stop ptsur-pub-ship
        >> sudo virsh stop ptsur-maps-ship

7. Expected Startup Results:

        Id    Name                           State
        ----------------------------------------------------
        1     ptsur-agg-ship              stopped
        2     ptsur-maps-ship             stopped
        4     ptsur-pub-ship              stopped

8. If you want to shutdown laconia you can do that now with:

        >> shutdown -h now

   Otherwise, exit your ssh connection to laconia to leave laconia up.

        >> exit

##### Shutdown Host

See #8 above
##### CORIOLIX Diagnostic

1. Check datapresence web user interfaces by using your browser.  Open a web browser and navigate to [https://192.168.1.162/status/](https://192.168.1.162/status/)

2. Expected result: You should see the datapresence user interface status page

3. Check status of UDP data capture:  
    1.  From webpage above, check upper right datetime and location display
    2.  Expected result: Date, Time, and Location (if moving all are updating)

4. Advanced diagnostic & unit tests - **TBD**
