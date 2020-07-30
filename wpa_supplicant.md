Wpa_supplicant
==============

### Contents
> 1. Overview
> 2. Terminology
> 3. Case study(94-test) 
> 4. Configure File
> 5. Useful Commands 
> 6. Reference

********
Overview
--------
WiFi stands for *Wireless Fidelity*, which means how accurate the signal is. The abbreviation, WiFi, can have different flavors.
 - How far can the wireless signal reach?
 - How much data can the signal send?
 - Is it backwards compatible with other standards?  

In the world of Wireless communication, we need a standard to regulate and unify how to communicate. Similar to power outlet, companies need to have a consensus about it so that we can plug in different kinds of electrical appliances. Therefore, **IEEE 802.1X** becomes the standard protocol when it comes to WiFi.

After we get the big pic of Wifi and its jargon, let's dive into *wpa_supplicant*. It can be considered as a cient edge when we wnat to connect an Access point(AP). Moreover, wpa_supplicant implements key negotiation with a WPA Authenticator and it controls the roaming and IEEE 802.11 authentication/association of the wlan driver.

Usually, wpa_supplicant works likely as a daemon program, and runs in the background continuously. Due to this characteristic, it has its own PID. It is crucial to kill the old PID when we want to launch a new one. Basically,using `ps aux | grep wpa && kill [PID]`. In addirion to wpa_supplicant, we can use *wpa_cli* or *wpa-gui* for a frontend programs. Before we want to start wpa_cli, we need to check our wireless interfaces and set our  configuration file.

Click [here](https://hostap.epitest.fi/wpa_supplicant/) for detail.

***********
Terminology
-----------
#### WEP 
 - Wired Encryption Protocol
 - Published in 1999
 - Replaced by WPA, TKIP
 - Old, flawed

#### WPA
 -  Wi-Fi Protected Access
 -  Published in 2003
 -  Replaced by WPA2
 -  WPA = IEEE 802.11i draft 3 = IEEE 802.1X/EAP + WEP/TKIP
 -  WPA Enterprise  
 -- A.K.A. WPA-EAP  
 -- Used in enterprise  
 -- Using **IEEE 802.1X/EAP**  
 -- Requires a Radius server  
  - WPA Personal  
 -- A.K.A. WPA-PSK  
 -- Used for individual  
 -- Using **Pre-Share-Key** instead of IEEE 802.1X/EAP  
 -- More vulnerable, but convenient  

#### WPA2
 -  Wi-Fi Protected Access II  
 -  A.K.A. RSN (Robust Security Network)
 -  Published in 2004  
 -  Dominant method  
 -  WPA2 = IEEE 802.11i = IEEE 802.1X/EAP + WEP/TKIP/CCMP  
 -  WPA2 Enterprise  
 -- A.K.A. WPA2-EAP  
 -- Used in enterprise  
 -- Using **IEEE 802.1X/EAP**  
 -- Requires a Radius server  
  - WPA2 Personal  
 -- A.K.A. WPA2-PSK  
 -- Used for individual  
 -- Using **Pre-Share-Key** instead of IEEE 802.1X/EAP  
 -- More vulnerable, but convenient  



#### WAP3
 - Wi-Fi Protected Access III 
 - Announced in 2018

#### TKIP
 - Temporal Key Integrity Protocol
 - An encryption way
 - An improvement of WEP
 - Replaced by CCMP
 - Published in 2002
 - Can apply to old hard ware

#### CCMP
 - Counter Mode Cipher Block Chaining Message Authentication Code Protocol 
 - An encryption way
 - An improvement of TKIP
 - Required new hard ware support
 - A.K.A. AES, AES-CCMP

#### AES
 -  Advanced Encryption Standard
 -  CCMP is based on AES

#### EAP
 - Extensible Authentication Protocol 
 - It's an extension protocol of IEEE802.1x
 - (compare to IEEE?)
 
#### SSID
 - service set identifier
 - BSSID: single ap
 - ESSID: multi ap

#### WPS
 - Wi-Fi Protected Setup
 - Easy to use for setup your WiFi

#### other
 - WPA and WPA2 have the same **mechanism and standard**, IEEE 802.1X/EAP, but using different **encryption**, TKIP or CCMP
 - *CCMP>TKIP>WEP*: These are all encryption methods.

#### example
```text
> wpa_cli
> scan
> scan_results
bssid / frequency / signal level/ flags / ssid
XX:XX:XX:XX:XX:XX    5240    -41    [WPA-PSK-CCMP+TKIP][WPA2-PSK-CCMP+TKIP][ESS][WPS]    94-test-5GHz
XX:XX:XX:XX:XX:XX    5240    -41    [WPA2-EAP-CCMP][ESS][WPS]    .M-Mobile
```
 - 94-test-5GHz: WPA2 and WPA personal encrypted by CCMP+TKIP and providing multi ap and wps
 - .M-Mobile: WPA2 enterprise encrypted by CCMP and providing multi ap and wps


******************
Case study(94-test)
-------------------

### steps

1. Connect to internet via ethernet    
 -- Download tools `apt-get install wireless-tool`    
2. Make sure you get wpasupplicant    
 -- `apt-get installl wpasupplicant`    
3. Enable wireless interface    
 -- `rfkill unblock wifi`    
> It is optional, depends on your enviroment. But it may be the reason of failure.    
4. Use iwconfig     
 -- `iwconfig` find the port    
5. bring the wifi port up    
 -- `ip link set [name] up` or `ifconfig [name] up`    
> In this case, you may get some error about the board. It's normal for this machine.    
6. Scan available wifi nearby    
 -- `iwlist scan | grep ESSID`    
7. Create config file    
 -- `vim /etc/wpa_supplicant/wpa_supplicant.conf`    
8. Finish the config file    
 ```txt
ctrl_interface=DIR=/run/wpa_supplicant
update_config=1

network={
    ssid="94-test-5GHz"
    proto=RSN WPA
    key_mgmt=WPA-PSK
    pairwise=CCMP TKIP
    group=CCMP TKIP
    psk="12345678"
}
 ```    
9. enable wpa_supplicant    
 -- `wpa_supplicant -B -i [interface name] -c /etc/wpa_supplicant/wpa_supplicant.conf`    
> -B option runs the daemon in the background.    
> -i option specifies the network interface to use.     
> -c option specifies the configuration file to be used.    
10. enter client console    
 -- `wpa_cli -i [interface name]`    
> Occasionally the wpa_cli doesn't get the right one. It's better to specify the network interface here. Also, if sth gets wrong, this may be the reason of the problem. Click [here](https://superuser.com/questions/1468973/wpa-cli-choose-interface-p2p-dev-wlan0-automatically-when-i-is-not-specified) for detail.    
11. interacting with wpa_supplicant with commands    
 -- `scan`: remember to do it before everything    
 -- `scan_results`: find your network    
 -- `list_networks`: list the number of available networks    
 -- `enable/disable_network [number]`    
 -- `status`: for checking    
 -- `add_netwotk`: also set ssid and password after this    
 -- `select_network [number]`    
 -- `terminate`: end the wpa_supplicant    
 -- `q`: end wpa_cli    
12. disable existence ethernet    
 -- `ip link set [port] down/up`    
> Bring down all the interface which is not related to WiFi, and bring up the WiFi interface at the same time.    
13. testing    
 -- `ping google.com`    


**************
Configure File
--------------
This file plays an important role in wpa_supplicant. In a nutshell, this file document how wpa_supplicant works and the information about networks. In detail, it contains following information: **network name and password**, **protocol**, **encryption method**, **hidden network information**, and **priority**.

click [here](https://www.daemon-systems.org/man/wpa_supplicant.conf.5.html) for detail

Typically, this file need to be start with this.    
```text
ctrl_interface=DIR=/run/wpa_supplicant GROUP=wheel
update_config=1
```
`/run/wpa_supplicant` can configure interface and connect to Wifi. You can set `GROUP=wheel` to any group you like, or ignore it. The file need to be created by root, placed under /etc/wpa_supplicant/, and named it anything ended with .conf. Then we need set `update_config` to 1 so that we can interate wpa_supplicant with wpa_cli.

Then, for the network part. You have three ways to finish this part. The first one is editting the config file directly. The second choice is using wpa_cli. The third one is using `wpa_passphrase`

##### Editting configure file  
This is the most direct way to achieve our goal.
syntax:
```text
network={
    variable1=description_1 description_2
    variable2=description_1 description_2
}
```
| variables | options | not set for default |
| ---- | ------- | ------------------- |
| ssid | "name of network"   | required |
| psk  | "password" | required |
| proto | WPA RSN | WPA RSN |
| key_mgmt | WPA-PSK WPA-EAP IEEE8021X NONE | WPA-PSK WPA-EAP |
| pairwise | CCMP TKIP | CCMP TKIP |
| group | CCMP TKIP WEP104 WEP40 | CCMP TKIP WEP104 WEP40 |



##### Using wpa_cli
After adding and setting the network, wpa_cli will suto save to config file.
```text
> wpa_cli
> set_network [variables]
> save config
```

##### Using passphass
This method is used to add the network which don't need to add additional variables except network name and password. Simply put, letting default for the rest variables. Passphrase can generate a set of password which is more confidential.
`wpa_passphrase your-ESSID your-passphrase | sudo tee /etc/wpa_supplicant.conf`


***************
Useful Commands
---------------
###### General:
> `ps aux | grep wpa && kill [PID]`    
> `apt-get install wireless-tool`    
> `apt-get installl wpasupplicant`    
> `rfkill unblock wifi`    
> `vim /etc/wpa_supplicant/wpa_supplicant.conf`    
> `ping google.com`

###### Interfaces and Network
> `iwconfig`    
> `ifconfig`    
> `ip link set [name] up/down` or `ifconfig [name] up/down`    
> `iwlist scan | grep ESSID`    

###### wpa_supplicant
> `wpa_supplicant -B -i [interface name] -c /etc/wpa_supplicant/wpa_supplicant.conf`

###### wpa_cli
> `wpa_cli -i [interface name]`
> `scan` `scan_result` `enable/disable_network` `list_network` `sellect_network` `terminate` `q` `add_network` `set_network`


*********
reference
---------
[wpa_supplicant document](https://hostap.epitest.fi/wpa_supplicant/)
[how to set up]( https://shapeshed.com/linux-wifi/)
[how to set up](https://www.linuxbabe.com/command-line/ubuntu-server-16-04-wifi-wpa-supplicant)
[how to set up](https://linuxconfig.org/how-to-connect-to-wifi-from-the-cli-on-debian-10-buster)
[useful commands](http://blog.changyy.org/2013/04/wpacli.html)
https://www.raspberrypi.org/documentation/configuration/wireless/wireless-cli.md
[config](https://b8807053.pixnet.net/blog/post/35964202)
[config](https://blog.xuite.net/tseng.jauming/baby/203326332-wpa_supplicant.conf範例設定)
[config_official document](https://www.daemon-systems.org/man/wpa_supplicant.conf.5.html)
[WPA_PSK](https://blog.csdn.net/a407603406/article/details/21343009?utm_medium=distribute.pc_relevant.none-task-blog-BlogCommendFromMachineLearnPai2-1.compare&depth_1-utm_source=distribute.pc_relevant.none-task-blog-BlogCommendFromMachineLearnPai2-1.compare)
[WPA-PSK, TKIP](https://www.howtogeek.com/204697/wi-fi-security-should-you-use-wpa2-aes-wpa2-tkip-or-both/)
