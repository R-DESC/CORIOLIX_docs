## Deployment - *R/V Endeavor* 2020

The information here covers the setup and installation information for the CORIOLIX system installed on the RCRV server Arcadia which was shipped to URI on June 4, 2020 (Update, Arrived June 11th!)

Tracking information for the UPS shipment may be found here: [1Z9839530357681372](http://www.ups.com/WebTracking/processInputRequest?loc=en_US&Requester=NES&tracknum=1Z9839530357681372&AgreeToTermsAndConditions=yes&WT.z_eCTAid=ct1_eml_Tracking__ct1_eml_qvn_eml_5shp&WT.z_edatesent=06042020)

NOTE - Let's just complete everything up to and including section 3 - Startup Host: Arcadia.  After that we should be able to remote in and complete the setup (and run some tests).  thanks, Chris

### 1 Background

#### Physical Equipment Information
* __*Server Make/Model:*__ Dell PowerEdge R440 1U Server
* __*Server Name:*__ Arcadia
* __*Server OS:*__ CentOS7

#### Account Information
* __*Local User Account:*__ rcrv
* __*Local User Account Password:*__ Please contact Chris or Jasmine

#### Network Information
Three Virtual Machines are run on the arcadia server  
1. endeavor-agg-ship - reads data from the network and writes data to local database  
2. endeavor-pub-ship - holds a copy of the data and services the shipboard web user interface  
3. map-endeavor-ship - a local mapserver to run the mapping/charting tools

Each machine is presented to the Endeavor shipboard wired network via a bridge on arcadia’s em1 interface.  Therefore, all 4 systems share one wired physical connection (only one network drop is required).  

__*PHYSICAL SERVER (arcadia):*__	192.168.2.9  
__*VM 1 (endeavor-agg-ship):*__	192.168.2.32  
__*VM 2 (endeavor-pub-ship):*__	192.168.2.33  
__*VM 3 (map-endeavor-ship):*__	192.168.2.34  

### 2 Installation Procedures
**Overview:**  The following procedure covers physical installation and cabling of the CORIOLIX server: arcadia on R/V Endeavor.

#### Install Host: Arcadia
1. Install arcadia in the Main Lab rack location where previously installed.  Secure with makeshift metal "tabs".
2. Connect power to both power supplies on the rear of the server.
3. Connect a network drop from the 192.168.2.0/24 network to eth1 on the arcadia.
4. Optionally, connect a keyboard, mouse, and display

### 3 Startup & Shutdown Procedures
**Overview:**
The following procedure covers starting the CORIOLIX system.  Please note that the host server arcadia is setup such that the CORIOLIX Virtual Machines start automatically.  The CORIOLIX services installed on each Virtual Machine are also set to start automatically.  This means that you only need to work though the first section "Startup Host: Arcadia" and may safely skip the subsequent startup steps.

Nonetheless, we'll include complete manual Startup procedures here to provide you the options and instructions for starting CORIOLIX VMs or CORIOLIX services manually.  Instructions for manual startup should be performed in sequence as presented.

For shutdown we currently advise that you follow the manual shutdown instructions under sections "Shutdown CORIOLIX services", "Shutdown VM's: 1, 2, & 3", and "Shutdown Host".  The virtualized system has proven to be robust to controlled shutdowns and restarts of arcadia (where the hypervisor manages the VM shutdowns) - but, we have additional testing to perform before we can be assured that this type of shytdown won't cause issue.

#### Startup Host: Arcadia
1. Press power button on front to start server, allows machine to boot.  
2. At CentOS login use the rcrv account  
3. Right click on the network connections icon in the upper right of the desktop.  
4. Set the em1 interface to be DHCP and check that the Endeavor DHCP server assigns the local **192.168.2.9** address to arcadia.  
5.	Test dns by pinging google (**ping 8.8.8.8**) or something like that – server needs to see the outside world.  
6.	If server gets expected IP (**192.168.2.9**) and can ping off-ship then we are good to start VM’s  

__*Note:*__ After completing the setup above and finishing with arcadia on the Endeavor network at 192.168.2.9 you no longer need to keep a keyboard, mouse, and monitor to work with Arcadia.  You can disconnect those input and display devices.  The remaining setup steps may be performed through an ssh connection to Arcadia

#### Startup VMs: 1, 2, & 3

1. From your local machine on the 192.168.2.0/24 network open a terminal window or your ssh client
2. Make an SSH connection from your local terminal to arcadia:

        >> ssh rcrv@192.168.2.9
        >> password: (enter user rcrv password)

3. Change to the root user:

        >> sudo su - root
        >> password: (enter user rcrv password)

4. List available virtual machines:

        >> virsh list --all

5. Expected List Results (id may vary):

        Id    Name                           State
        ----------------------------------------------------
        1     endeavor-agg-ship              stopped
        2     endeavor-map-ship              stopped
        4     endeavor-pub-ship              stopped

6. Start each VM from the command line (*Note*: VM startups usually take less about 60-90 seconds each.):

        >> sudo virsh start endeavor-agg-ship
        >> sudo virsh start endeavor-pub-ship
        >> sudo virsh start endeavor-map-ship

7. Expected Startup Results:

        Id    Name                           State
        ----------------------------------------------------
        1     endeavor-agg-ship              running
        2     endeavor-map-ship              running
        4     endeavor-pub-ship              running

8. Exit your ssh connection to arcadia

        >> exit

##### Startup CORIOLIX services

The following services must be manually started on each virtual machine:

vm: endeavor-agg-ship | vm: endeavor-pub-ship
--- | ---
service: sym_service | service: sym_service
service: coriolix | service: coriolix
service: mqtt | *   

*There is no mqtt service on endeavor-pub-ship

To start CORIOLIX follow the workflow below.  You might find it helpful to open two ssh connections, one to each vm.

1. Start SymmetricDS

    Connect to endeavor-pub-ship via ssh, leave ssh session open after running commands below.

        >> ssh rcrv@192.168.2.33
        >> password: (use rcrv account password)
        >> sudo /opt/SymmetricDS/symmetric-server-3.9.14/bin/sym_service start
        leave ssh session open (for step 2)

    Connect to endeavor-agg-ship via ssh, leave ssh session open after running commands below.

        >> ssh rcrv@192.168.2.32
        >> password: (use rcrv account password)
        >> sudo /opt/SymmetricDS/symmetric-server-3.9.14/bin/sym_service start
        leave ssh session open (for step 2)

2. Start CORIOLIX Services

    From your open connection to endeavor-pub-ship, execute:

        >> sudo systemctl start coriolix
        >> exit

    From your open connection to endeavor-agg-ship, execute:

        >> sudo systemctl start coriolix; sudo systemctl start mqtt
        >> sudo systemctl status coriolix
        >> exit

##### Shutdown CORIOLIX services

Shutdown procedures are basically the startup procedures in reverse.

1. Shutdown CORIOLIX

    Connect to endeavor-agg-ship via ssh, leave ssh session open after running commands below.

        >> ssh rcrv@192.168.2.32
        >> password: (use rcrv account password)
        >> sudo sudo systemctl stop coriolix; sudo systemctl stop mqtt
        leave ssh session open (for step 2)

    Connect to endeavor-pub-ship via ssh, leave ssh session open after running commands below.

        >> ssh rcrv@192.168.2.33
        >> password: (use rcrv account password)
        >> sudo systemctl stop coriolix
        leave ssh session open (for step 2)

2. Stop SymmetricDS

    From your open connection to endeavor-agg-ship, execute:

        >> sudo /opt/SymmetricDS/symmetric-server-3.9.14/bin/sym_service stop
        >> exit

    From your open connection to endeavor-pub-ship, execute:

        >> sudo /opt/SymmetricDS/symmetric-server-3.9.14/bin/sym_service stop

##### Shutdown VM's: 1, 2, & 3

The VM shutdown process is also essentially the startup process in reverse.

1. From your local machine on the 192.168.2.0/24 network open a terminal window or your ssh client
2. Make an SSH connection from your local terminal to arcadia:

        >> ssh rcrv@192.168.2.9
        >> password: (enter user rcrv password)

3. Change to the root user:

        >> sudo su - root
        >> password: (enter user rcrv password)

4. List available virtual machines:

        >> virsh list --all

5. Expected List Results (id may vary):

        Id    Name                           State
        ----------------------------------------------------
        1     endeavor-agg-ship              running
        2     endeavor-map-ship             running
        4     endeavor-pub-ship              running

6. Start each VM from the command line (*Note*: VM startups usually take less about 60-90 seconds each.):

        >> sudo virsh stop endeavor-agg-ship
        >> sudo virsh stop endeavor-pub-ship
        >> sudo virsh stop endeavor-map-ship

7. Expected Startup Results:

        Id    Name                           State
        ----------------------------------------------------
        1     endeavor-agg-ship              stopped
        2     endeavor-map-ship             stopped
        4     endeavor-pub-ship              stopped

8. If you want to shutdown arcadia you can do that now with:

        >> shutdown -h now

   Otherwise, exit your ssh connection to arcadia to leave arcadia up.

        >> exit

##### Shutdown Host

See #8 above
##### CORIOLIX Diagnostic

1. Check datapresence web user interfaces by using your browser.  Open a web browser and navigate to [https://192.168.2.33:8001/status/](https://192.168.2.33:8001/status/)

2. Expected result: You should see the datapresence user interface status page

3. Check status of UDP data capture:  
    1.  From webpage above, check upper right datetime and location display
    2.  Expected result: Date, Time, and Location (if moving all are updating)

4. Advanced diagnostic & unit tests - **TBD**
