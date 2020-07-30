Wpa_supplicant
==============

### Table of Contents
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

Usually, wpa_supplicant works likely as a daemon program, and runs in the background continuously. Due to this characteristic, it has its own PID. It is crucial to kill the old PID when we want to launch a new one. Basically, using `ps aux | grep wpa && kill [PID]`. In addirion to wpa_supplicant, we can use **wpa_cli** or *wpa-gui* as a frontend program. Before we want to start wpa_cli, we need to check our wireless interfaces and set our configuration file properly.

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
 -  WPA = **IEEE 802.11i draft 3 = IEEE 802.1X/EAP + WEP/TKIP**
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
 -  WPA2 = **IEEE 802.11i = IEEE 802.1X/EAP + WEP/TKIP/CCMP**    
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
 
#### SSID
 - service set identifier
 - BSSID: single ap
 - ESSID: multi ap

#### WPS
 - Wi-Fi Protected Setup
 - Easy to use for setting up your WiFi

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


*******************
Case study(94-test)
-------------------
In this practice, I use 94-test-5GHz, which is one of APs in our office. The AP is WPA/WPA2-PSK with CCMP+TKIP. We can get this information by using wpa_cli.

### steps
1. Connect to internet via ethernet and get essential tools    
 -- Download tools `apt-get install wireless-tools`    
```text
root@Moxa:~# apt-get install wireless-tools
Reading package lists... Done
Building dependency tree
Reading state information... Done
wireless-tools is already the newest version (30~pre9-12+b1).
0 upgraded, 0 newly installed, 0 to remove and 1 not upgraded.
root@Moxa:~#
```
2. Make sure you get wpasupplicant    
 -- `apt-get install wpasupplicant`    
```text
root@Moxa:~# apt-get install wpasupplicant
Reading package lists... Done
Building dependency tree
Reading state information... Done
wpasupplicant is already the newest version (2:2.4-1+deb9u6).
0 upgraded, 0 newly installed, 0 to remove and 1 not upgraded.
root@Moxa:~#
```
3. Enable wireless interface    
 -- `rfkill unblock wifi`    
```text
root@Moxa:~# rfkill unblock wifi
root@Moxa:~#
```
> It is optional, depends on your enviroment. But it may be the reason of failure.    
4. Use iwconfig     
 -- `iwconfig` find the available port    
 ```text
 root@Moxa:~# iwconfig
wlp2s0    IEEE 802.11  ESSID:off/any
          Mode:Managed  Access Point: Not-Associated   Tx-Power=30 dBm
          Retry short limit:7   RTS thr:off   Fragment thr:off
          Encryption key:off
          Power Management:on

lo        no wireless extensions.

enp1s0    no wireless extensions.

enp0s31f6  no wireless extensions.

root@Moxa:~#
``
5. bring the wifi interface up    
 -- `ip link set [name] up` or `ifconfig [name] up`    
```text
root@Moxa:~# ip link set wlp2s0 up
root@Moxa:~# ifconfig wlp2s0 up
root@Moxa:~#
```
> In this case, you may get some error about the board. It's normal for this machine.    
6. Scan available wifi nearby    
 -- `iwlist scan | grep ESSID`    
 ```text
 root@Moxa:~# iwlist scan | grep ESSID
                    ESSID:"dlink_24GHz_luke"
                    ESSID:"TP-LINK_5ABDA7"
                    ESSID:"DIR-615-1936-CH"
                    ESSID:"\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00"
                    ESSID:"moxaisd"
                    ESSID:"moxa-sample-ap"
                    ESSID:"ASUS"
                    ESSID:"94-test"
                    ESSID:"Xiaomi_1FE4"
                    ESSID:"AWGTEST"
                    ESSID:".M-Guest"
                    ESSID:""
                    ESSID:".M-Mobile"
                    ESSID:".M-Guest"
                    ESSID:"RT-AC56U_2.4G"
                    ESSID:"OwenTEST"
                    ESSID:".M-Mobile"
                    ESSID:".M-Guest"
                    ESSID:".M-Mobile"
                    ESSID:".M-Guest"
                    ESSID:".M-Mobile"
                    ESSID:".M-Guest"
                    ESSID:"RT-AC56UM-5G"
                    ESSID:"moxaisd-5g"
                    ESSID:"OwenTEST_5G"
                    ESSID:"NETGEAR-5G"
                    ESSID:"Xiaomi_1FE4_5G"
                    ESSID:"\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00"
                    ESSID:"burnin_a"
                    ESSID:"94COOL"
                    ESSID:"\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00"
                    ESSID:".M-Mobile"
                    ESSID:".M-Guest"
                    ESSID:".M-Mobile"
                    ESSID:"RT-AC56UM"
                    ESSID:"burnin"
                    ESSID:".M-Mobile"
                    ESSID:"domi-gw"
                    ESSID:"RBC-M2G"
                    ESSID:"RBC-C2G"
                    ESSID:"IBR900-2eb-5g"
                    ESSID:".M-Mobile"
                    ESSID:".M-Guest"
                    ESSID:"RT-AC56U_5G"
lo        Interface doesn't support scanning.

enp1s0    Interface doesn't support scanning.

enp0s31f6  Interface doesn't support scanning.

root@Moxa:~#
```
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
 ```text
 root@Moxa:~# wpa_supplicant -B -i wlp2s0 -c /etc/wpa_supplicant/wpa_supplicant.conf
Successfully initialized wpa_supplicant
root@Moxa:~#
```
> -B option runs the daemon in the background.    
> -i option specifies the network interface to use.     
> -c option specifies the configuration file to be used.    
10. enter client console    
 -- `wpa_cli -i [interface name]`    
 ```text
 root@Moxa:~# `wpa_cli -i wlp2s0
> `wpa_cli -i wlp2s0^C
root@Moxa:~# wpa_cli -i wlp2s0
wpa_cli v2.4
Copyright (c) 2004-2015, Jouni Malinen <j@w1.fi> and contributors

This software may be distributed under the terms of the BSD license.
See README for more details.



Interactive mode

>
```
> Occasionally the wpa_cli doesn't get the right one. It's better to specify the network interface here. Also, if sth gets wrong, this may be the reason of the problem. Click [here](https://superuser.com/questions/1468973/wpa-cli-choose-interface-p2p-dev-wlan0-automatically-when-i-is-not-specified) for detail.    
11. interacting with wpa_supplicant with commands    
 -- `scan`: remember to do it before adding new networks     
 -- `scan_results`: list available AP    
 -- `list_networks`: list the number of available networks    
 -- `enable/disable_network [network number]`    
 -- `status`: for checking    
 -- `add_netwotk`: also set ssid and password after this    
 -- `select_network [network number]`    
 -- `terminate`: end the wpa_supplicant    
 -- `q`: end wpa_cli    
> In the following demo, I use the wpa_cli to add another network,94-test.
```text
root@Moxa:~# `wpa_cli -i wlp2s0
> `wpa_cli -i wlp2s0^C
root@Moxa:~# wpa_cli -i wlp2s0
wpa_cli v2.4
Copyright (c) 2004-2015, Jouni Malinen <j@w1.fi> and contributors

This software may be distributed under the terms of the BSD license.
See README for more details.



Interactive mode

> scan
OK
<3>CTRL-EVENT-SCAN-STARTED
<3>CTRL-EVENT-SCAN-RESULTS
> scan_results
bssid / frequency / signal level / flags / ssid
7c:57:3c:2e:d5:f1       5180    -44     [WPA2-EAP-CCMP][ESS]    .M-Mobile
7c:57:3c:2e:d5:f2       5180    -44     [WPA2-PSK-CCMP][ESS]    .M-Guest
f0:79:59:e3:06:0c       5745    -46     [WPA2-PSK-CCMP][WPS][ESS]       RT-AC56U                               _5G
74:da:da:35:72:6e       5765    -50     [WPA-PSK-CCMP+TKIP][WPA2-PSK-CCMP+TKIP][                               WPS][ESS]       94-test-5GHz
28:6c:07:64:1f:e6       5785    -62     [WPA-PSK-CCMP+TKIP][WPA2-PSK-CCMP+TKIP][                               WPS][ESS]       Xiaomi_1FE4_5G
7c:57:3c:2e:5e:b2       5180    -67     [WPA2-PSK-CCMP][ESS]    .M-Guest
c4:e9:84:43:41:71       5745    -66     [WPA2-PSK-CCMP][WPS][ESS]       OwenTEST                               _5G
7c:57:3c:30:77:f1       5240    -72     [WPA2-EAP-CCMP][ESS]    .M-Mobile
7c:57:3c:30:77:f2       5240    -73     [WPA2-PSK-CCMP][ESS]    .M-Guest
7c:57:3c:2e:ba:12       5280    -76     [WPA2-PSK-CCMP][ESS]    .M-Guest
7c:57:3c:2e:ba:11       5280    -75     [WPA2-EAP-CCMP][ESS]    .M-Mobile
f8:d1:11:5a:bd:a7       2412    -50     [WPA-PSK-CCMP][WPA2-PSK-CCMP][ESS]     T                               P-LINK_5ABDA7
f0:b4:d2:60:19:37       2412    -59     [WPA2-PSK-CCMP][ESS]    DIR-615-1936-CH
c8:3a:35:09:30:f1       2457    -66     [WPA-PSK-CCMP][WPA2-PSK-CCMP][WPS][ESS]\                               x00\x00\x00\x00\x00\x00\x00
b0:b2:dc:dd:c9:e4       2412    -76     [WPA2-PSK-CCMP][WPS][ESS]       94COOL
60:a4:4c:6b:4b:2a       2412    -53     [WPA2-PSK-CCMP+TKIP][ESS]       \x00\x00                               \x00\x00\x00\x00\x00\x00\x00\x00
74:da:da:35:72:6c       2437    -54     [WPA-PSK-CCMP+TKIP][WPA2-PSK-CCMP+TKIP][                               WPS][ESS]       94-test
70:62:b8:64:21:10       2412    -59     [WPA-PSK-CCMP+TKIP][WPA2-PSK-CCMP+TKIP][                               WPS][ESS]       dlink_24GHz_luke
28:6c:07:64:1f:e5       2442    -60     [WPA-PSK-CCMP+TKIP][WPA2-PSK-CCMP+TKIP][                               WPS][ESS]       Xiaomi_1FE4
0c:9d:92:b1:de:20       2427    -63     [WPA2-PSK-CCMP][WPS][ESS]       moxaisd
10:c3:7b:c6:47:f8       2417    -79     [WPA2-PSK-CCMP][WPS][ESS]       RT-AC56U                               M
00:18:e7:f4:be:8a       2437    -70     [WPA-PSK-CCMP+TKIP][WPA2-PSK-CCMP+TKIP][                               ESS]    AWGTEST
6e:c7:ec:e9:4e:25       2412    -79     [WPA2-PSK-CCMP][ESS]    AndroidAP4e25
7c:57:3c:2e:d5:e2       2437    -53     [WPA2-PSK-CCMP][ESS]    .M-Guest
7c:57:3c:2e:d5:e1       2437    -53     [WPA2-EAP-CCMP][ESS]    .M-Mobile
7c:57:3c:2e:6a:c1       2437    -62     [WPA2-EAP-CCMP][ESS]    .M-Mobile
7c:57:3c:2e:6a:c2       2437    -61     [WPA2-PSK-CCMP][ESS]    .M-Guest
00:15:61:20:8b:d6       2437    -67     [WPA2-PSK-CCMP][ESS]    moxa-sample-ap
94:e3:6d:79:54:8d       2437    -69     [WPA-PSK-CCMP+TKIP][WPA2-PSK-CCMP][ESS]d                               omi-gw
00:4e:35:a3:f6:42       2412    -78     [WPA2-PSK-CCMP][ESS]    .M-Guest
00:4e:35:a3:f6:41       2412    -79     [WPA2-EAP-CCMP][ESS]    .M-Mobile
c8:3a:35:09:42:b5       5785    -71     [WPA-PSK-CCMP][WPA2-PSK-CCMP][ESS]     \                               x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00
60:a4:4c:6b:4b:2c       5180    -74     [WPA2-PSK-CCMP+TKIP][ESS]       \x00\x00                               \x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00
54:04:a6:bd:8c:88       2437    -75     [WPA2-PSK-CCMP][ESS]    ASUS
7c:57:3c:2f:bf:f2       5220    -82     [WPA2-PSK-CCMP][ESS]    .M-Guest
10:c3:7b:c6:47:fc       5745    -74     [WPA2-PSK-CCMP][WPS][ESS]       RT-AC56U                               M-5G
7c:57:3c:2f:bf:f1       5220    -82     [WPA2-EAP-CCMP][ESS]    .M-Mobile
7c:57:3c:2e:7a:91       5280    -82     [WPA2-EAP-CCMP][ESS]    .M-Mobile
00:4e:35:a3:f6:51       5200    -82     [WPA2-EAP-CCMP][ESS]    .M-Mobile
7c:57:3c:2e:7a:92       5280    -84     [WPA2-PSK-CCMP][ESS]    .M-Guest
f0:79:59:e3:06:08       2462    -44     [WPA2-PSK-CCMP][WPS][ESS]       RT-AC56U                               _2.4G
c4:e9:84:43:41:72       2462    -69     [WPA-PSK-CCMP][ESS]     OwenTEST
7c:57:3c:2e:e1:22       2462    -65     [WPA2-PSK-CCMP][ESS]    .M-Guest
7c:57:3c:30:77:e1       2462    -78     [WPA2-EAP-CCMP][ESS]    .M-Mobile
7c:57:3c:30:77:e2       2462    -78     [WPA2-PSK-CCMP][ESS]    .M-Guest
54:04:a6:bd:8c:89       2437    -71     [ESS]   ASUS
06:90:e8:6c:64:1b       5180    -84     [ESS]   burnin_a
a0:40:a0:82:48:cc       5765    -47     [WPA2-PSK-CCMP][WPS][ESS]       NETGEAR-                               5G
de:d9:63:42:e6:7c       2452    -78     [WPA2-PSK-CCMP][ESS]    Pixel_8766
1c:5f:2b:70:ef:90       2457    -76     [WPA-PSK-CCMP+TKIP][WPA2-PSK-CCMP+TKIP][                               WPS][ESS]       RBC-M2G
7c:57:3c:2e:e1:21       2462    -66     [WPA2-EAP-CCMP][ESS]    .M-Mobile
7c:57:3c:2e:ba:01       2437    -74     [WPA2-EAP-CCMP][ESS]    .M-Mobile
7c:57:3c:2e:ba:02       2437    -74     [WPA2-PSK-CCMP][ESS]    .M-Guest
00:30:44:43:32:ed       5220    -91     [WPA2-PSK-CCMP][ESS]    IBR900-2eb-5g
> list_networks
network id / ssid / bssid / flags
0       94-test-5GHz    any     [CURRENT]
> add_network
1
> set_network 1 ssid "94-test"
OK
> set_network 1 psk "12345678"
OK
> list_networks
network id / ssid / bssid / flags
0       94-test-5GHz    any     [CURRENT]
1       94-test any     [DISABLED]
> enable_network 1
OK
> list_networks
network id / ssid / bssid / flags
0       94-test-5GHz    any     [CURRENT]
1       94-test any
> select_network 1
OK
<3>CTRL-EVENT-DISCONNECTED bssid=74:da:da:35:72:6e reason=3 locally_generated=1
<3>CTRL-EVENT-SCAN-STARTED
<3>CTRL-EVENT-SCAN-RESULTS
<3>WPS-AP-AVAILABLE
<3>SME: Trying to authenticate with 74:da:da:35:72:6c (SSID='94-test' freq=2437 MHz)
<3>Trying to associate with 74:da:da:35:72:6c (SSID='94-test' freq=2437 MHz)
<3>Associated with 74:da:da:35:72:6c
<3>WPA: Key negotiation completed with 74:da:da:35:72:6c [PTK=CCMP GTK=TKIP]
<3>CTRL-EVENT-CONNECTED - Connection to 74:da:da:35:72:6c completed [id=1 id_str=]
> status
bssid=74:da:da:35:72:6c
freq=2437
ssid=94-test
id=1
mode=station
pairwise_cipher=CCMP
group_cipher=TKIP
key_mgmt=WPA2-PSK
wpa_state=COMPLETED
ip_address=192.168.0.109
p2p_device_address=00:15:61:20:a2:f9
address=00:15:61:20:a2:f9
uuid=06e6bd41-31b2-5725-aea0-9de37e96668f
<3>CTRL-EVENT-SCAN-STARTED
<3>CTRL-EVENT-SCAN-RESULTS
>
```
12. disable existence ethernet    
 -- `ip link set [port] down`    
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
`/run/wpa_supplicant` can configure interface and connect to Wifi. You can set `GROUP=wheel` to any group you like, or ignore it. The file need to be created by root, placed under /etc/wpa_supplicant/, and named it anything ended with .conf. Then we need set `update_config` to 1 so that we can interact wpa_supplicant with wpa_cli.

Then, for the network part. You have three ways to finish this part. The first one is editting the config file directly. The second choice is using wpa_cli. The third one is using `wpa_passphrase`

##### Editting the config file:
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
##### Using wpa_cli:
After adding and setting the network, wpa_cli will suto save to config file.
```text
> wpa_cli
> set_network [variables]
> save config
```
##### Using passphass:
This method is used to add the network which don't need to add additional variables except network name and password. Simply put, letting default for the rest of variables. Passphrase can generate a set of password which is more confidential.    
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
###### Interfaces and Network:
> `iwconfig`    
> `ifconfig`    
> `ip link set [name] up/down` or `ifconfig [name] up/down`    
> `iwlist scan | grep ESSID`    
###### wpa_supplicant:
> `wpa_supplicant -B -i [interface name] -c /etc/wpa_supplicant/wpa_supplicant.conf`
###### wpa_cli:
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
[config](https://b8807053.pixnet.net/blog/post/35964202)    
[config](https://blog.xuite.net/tseng.jauming/baby/203326332-wpa_supplicant.conf範例設定)    
[config_official document](https://www.daemon-systems.org/man/wpa_supplicant.conf.5.html)    
[WiFi](https://blog.csdn.net/a407603406/article/details/21343009?utm_medium=distribute.pc_relevant.none-task-blog-BlogCommendFromMachineLearnPai2-1.compare&depth_1-utm_source=distribute.pc_relevant.none-task-blog-BlogCommendFromMachineLearnPai2-1.compare)    
[WiFi](https://www.howtogeek.com/204697/wi-fi-security-should-you-use-wpa2-aes-wpa2-tkip-or-both/)   
[WiFi](https://www.netspotapp.com/explaining-wifi-standards.html)
https://www.raspberrypi.org/documentation/configuration/wireless/wireless-cli.md
