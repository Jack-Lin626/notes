Wpa_supplicant
==============

### Contents
> 1. Overview
> 2. Case study(94-test) 
> 2. Configs
> 3. Commands 
> 4. reference

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
 - Required new hard ware
 - A.K.A. AES, AES-CCMP

#### AES
 -  Advanced Encryption Standard
 -  CCMP is based on AES

#### EAP
 - Extensible Authentication Protocol 
 - 
 
#### SSID
 - service set identifier
 - BSSID: single ap
 - ESSID: multi ap

******************
Case study(94-test)
-------------------

steps
-----
1. connect to internet via ethernet
 -- Download tools `apt-get install wireless-tool`
2. make sure you get wpasupplicant
 -- `apt-get installl wpasupplicant`
3. using iwconfig 
 -- `iwconfig` find the port
4. bring the wifi port up
 -- `ip link set [name] up` or `ifconfig [name] up`
5. scan available wifi nearby
 -- `iwlist scan | grep ESSID`
6. Creating config file
 -- `vim /etc/wpa_supplicant/wpa_supplicant.conf`
7. finish the config file
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
8. enable wpa_supplicant
 -- `wpa_supplicant -B -i [port name] -c /etc/wpa_supplicant/wpa_supplicant.conf`
9. enter client console
 -- `wpa_cli -i [port name]`
 -- Sometimes the wpa_cli will not get the right one. It's better to specify the info here. Also, if sth gets wrong, this may be the reason of the problem.
10. interacting with wpa with commands
 -- `scan`
 -- `status`
 -- `list_networks` 
 -- `enable/disable_network [number]`
 -- `scan_results`
 -- `select_network [number]`
 -- `add_netwotk`
11. disable existence ethernet
 -- `ip link set [port] down`
12. testing
 -- `ping google.com`

see $ sudo vi /etc/network/interfaces
$ sudo /etc/init.d/networking restart





second: install wireless tool
rfkill crucial````


*********
reference
---------
https://hostap.epitest.fi/wpa_supplicant/
https://shapeshed.com/linux-wifi/
http://blog.changyy.org/2013/04/wpacli.html
https://www.raspberrypi.org/documentation/configuration/wireless/wireless-cli.md
https://www.linuxbabe.com/command-line/ubuntu-server-16-04-wifi-wpa-supplicant
https://b8807053.pixnet.net/blog/post/35964202
https://blog.xuite.net/tseng.jauming/baby/203326332-wpa_supplicant.conf範例設定
https://www.daemon-systems.org/man/wpa_supplicant.conf.5.html
https://linuxconfig.org/how-to-connect-to-wifi-from-the-cli-on-debian-10-buster
http://simple-is-beauty.blogspot.com/2017/10/wifi-directwi-fi-p2p.html
https://blog.csdn.net/a407603406/article/details/21343009?utm_medium=distribute.pc_relevant.none-task-blog-BlogCommendFromMachineLearnPai2-1.compare&depth_1-utm_source=distribute.pc_relevant.none-task-blog-BlogCommendFromMachineLearnPai2-1.compare
https://www.howtogeek.com/204697/wi-fi-security-should-you-use-wpa2-aes-wpa2-tkip-or-both/













