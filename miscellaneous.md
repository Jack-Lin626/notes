Contents
========

> internet setting
> local apt server
> GIT
--------

# internet setting

## STEPS

1. checking which interface are connected     
 -- see the information from `ip a` dirrectly    
 -- using `dmesg | grep enp`     
 -- using `mii-tool [port name]` to see detail     

2. modify the config file    
 -- `vim /etc/network/interfaces`    
 -- or using ip cammand to set the configure    
 -- command the unrelated port    
> e.g.     
> auto enp1s0    
> iface enp1s0 inet static    
>&emsp;&emsp; address 10.144.54.133    
>&emsp;&emsp; network 192.168.4.0    
>&emsp;&emsp; netmask 255.255.252.0    
>&emsp;&emsp; gateway 10.144.55.254    
>&emsp;&emsp; broadcast 192.168.4.255    

3. bring the internet up    
 -- `inconfig [name] up`    
 -- `ip linkset [name] up/down`    

# local apt wserver

## big pic
 - contrary to apt server in remote    
## STEPS: preset

Set up internet when we use a plain machine    
`ip addr add 10.144.54.134/22 dev eth0`    
`ip route add default via 10.144.55.254 dev eth0`    

Update    
`apt-get update`    



install crucial tool    
`sudo apt-get install dpkg-dev`    

create local repository folder    
`sudo mkdir -p /usr/local/mydebs`    

create local update mechanism    
`mkdir -p ~/bin/`    
`vim ~/bin/update-mydebs`   
```vim
#! /bin/bash
 cd /usr/local/mydebs
 dpkg-scanpackages . /dev/null | gzip -9c > Packages.gz
 ```
 `chmod u+x ~/bin/update-mydebs`    
 `vim /etc/apt/sources.list`   
 add    
 `deb file:/usr/local/mydebs ./`    

## STEPS: move file

move application to local repo    
`mv /tmp/uc2100-base-system_1.5.2_armhf.deb /usr/local/mydebs/`    

## STEPS: start    
`~/bin/update-mydebs`    
`sudo apt-get update`    
`apt-get install uc2100-base-system`    
  
## reference    
https://wiki.debian.org/MaintainerScripts  
https://askubuntu.com/questions/170348/how-to-create-a-local-apt-repository


# GIT
the purpose of git command    
:    
to cut a useful version point   
every commit should be specific     

