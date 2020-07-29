Wpa_supplicant
=======

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

As we can see, 


*******************
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














