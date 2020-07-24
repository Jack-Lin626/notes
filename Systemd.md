Systemd
=======

### Contents
> 1. Overview 
> 2. Big pic about how it works
> 3. Environment
> 4. rules
> 5. Codes 
> 6. Systemctl commands 

********
Overview
--------
In Linux environment, **systemd** is used to manage the service and system. The existence of systemd is to replace the old mechanism, System V Init. The features of systemd are enormous. I will save my words in this part. If you are interested about this, you can google "systemd V.S. System V Init".

I just list some features here.

* First process to start and last to stop
* Dependency Control
* Easier to Track and Restart Services
* Better Logging, Debugging and Profiling

The tasks run by systemd is called **units**. There are many kinds of units, such as .service, .mount, .device, .socket, or .timer. **Targets** are groups of units. For instance, graphical.target calls all units that are related to graphical user interface. Also, Targets are applied to dependency control as well.

There are three files in my systemd daemon demonstration. A .service file is used to describe this service and its charactristics. A shell script, this file specifically contains the detail process of this service. Lastly, a txt file, the shell script will use it to store the number of reboot time.

After placeing these files in the right place, we can use `systemctl` to control and inspect this service.

**************************
Big pic about how it works
--------------------------
Systemd is the **parents** of all other precesses. In the process of systemd, it will load all unitfiles.

```text
0s _____ 1s _____ 2s _____ 3s _____ 4s _____ 5s _____ 6s _____ 7s ___
|<-----kernel----->|  
                   |<--systemd---------------------------------------   
                                 |<--load services-------------------
                                     |<--load other services---------
```

Next step, you will know how a unit works in a practical way. 

*First*, creating a **unit** secion, you need to clarify the dependency in this phace. *Second*, linking your scripts and knowing the order of your task are of importance in **service** section. *Third*, filling WantedBy with the corresponding runlvel in **install** section.

Then, I will explain what happened behind the screen after a unit was created.

After reloading and enabling a unit, it will call scripts showed in the service section. When systemd enables this service, it will create a file in /etc/systemd/system/XXX.target.wants/, which is related to the argument in the install section of the unit file. We can easily check this file if the service doesn't work as expectation. As soon as the unit is activated, the unit file will follow the dependdency and do its works in the right time.

```text
                             ___________
        <exec>---------------|unit file|----------------<dependency>
          |                       |                          |
          |                       |                          |
      _________               <create>                 ______________
      |scripts|                   |                    |unit section|  
                                  |
                  ______________________________________
                  |/etc/systemd/system/XXX.target.want/|
```

**************************
Environment and presetting
-----------
In this practice, I use Debian 9, and I have addjusted it to Debian 10. 

*****
Rules
-----
There are three sections, which are [unit], [service], and [install], in every unit file. The essence of systemd  is how to write a unit file precisely and effectly. See <https://www.freedesktop.org/software/systemd/man/systemd.service.html> for extraordinarily verbose detail.

[unit]
> Here we use `Requires/Wants/After/Before` to *establish the dependencies*. `After` and `Before` clear the order of tasks. `Requires` means strong dependecy, which means the system must wait untill the unit been built. On the other hand, `Wants` allows the failure, namely the weak dependency. 
>> - [Description] = Unit description
>> - [Documentation] = Documentation 
>> - [linksRequires] = Additional units required 
>> - [Before/After] = Unit must start 
>> - [Before/AfterWants] = Weaker Requires
>> - [Conflicts] = Units cannot co-exist
>> - [WantedBy/RequiredBy] = Set other units requirement

[service]
> They are two concepts we need to deal with in this section. The first is `type`. Basically, you can just use oneshot or simple. See the link for detail. The second one is the **action of this task**. You need to specify which scripts to execuate. After that, you need consider how long will your task exist. Or, do you have and additional command that needs to executed after or before the script?
>> - [Type] = simple, exec, forking, oneshot, dbus, notify or idle
>> - [RemainAfterExit] = yes for considering active even when all its processes exited. Defaults to no.
>> - [PIDFile] = Takes a path referring to the PID file of the service.
>> - [ExecStart] = Path of the script
>> - [RemainAfterExit] = commonly used with the oneshot type, the service should be considered active even after the process exits.
>> - [ExecStartPre/Post] = Additional commands that are executed before/ after or after the command in [ExecStart].

[install]
> It carries installation information for the unit. Search runlevel for detail. 
>> - [WantedBy] = Usually use `multi-user.target`

*****
Codes
-----
First, we create the service file in /lib/systemd/system/reboot_test.service.
```
    [Unit]
    Description=if machine can connect to internet, it can reboot for 5 times.
    Wants=networking.service NetworkManager-wait-online.service
    After=networking.service NetworkManager-wait-online.service
    
    [Service]
    Type=oneshot
    ExecStart=/usr/sbin/reboot_test.sh
    RemainAfterExit=yes
    
    [Install]
    WantedBy=multi-user.target 
```
Second, we create a script, actually, you can use any favorite language you like. I place this in /usr/sbin/reboot_test.sh.
```bash
    #!/bin/bash
    #Date: 7/14/20
    #Author: Jack Lin
    #Description: 
    #This scriipt will test the internet connection first.
    #Then, it will reopen if connecting successfully for five times.
    #Counting file is in /usr/sbin/count.txt.
    
    last=$(cat /usr/sbin/count.txt)
    
    echo "${last}"
    
    new=$((${last}+1))
    
    echo "${new}" > "/usr/sbin/count.txt"
    
    ping google.com -c 5
    
    return=$(echo $?)
    
    if [ $return -eq 0 ]; then
    
    	if [ $new -le 5 ] ; then
    		reboot
    	else
    		echo "0" > "/usr/sbin/count.txt"
    		exit 0
    	fi
    else 
    	wall "no internet connection"
    fi
```
The last file is a storage file for the script. Simply run `touch /usr/sbin/count.txt && echo 0 > /usr/sbin/count.txt`



******************
Systemctl commands
------------------
To fully understand and harness the power of systemd, you must be familiar with this command. After systemd substitude and inherit the role of sysvinit, it provides more convenient and versatile functions for us to debug and profile our service and system. 

Here, I list some useful combination of commands.
> `systemctl daemon-reload`
>> - Reloading daemons after you make any change.

> `systemctl status/enable/restart/disable [name].service`
>> - Handy when you are testing new daemon. 

> `systemctl --type service`
>> - Listing all service/target/socket.

> `touch /tmp/boot_plot.svg && systemd-analyze plot > /tmp/boot_plot.svg`
>> - Profiling all service in booting stage to a blank file. 
>> - Useful to see the dependencies of daemons. 
>> - Rendered in a broweser. 

>  `journalctl`
>> - Improved debugging and profiling. 
>> - Combining with any filter to get your desired logs.










