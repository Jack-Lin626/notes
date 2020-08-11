Contents
========

> internet setting

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


local apt repo
https://askubuntu.com/questions/170348/how-to-create-a-local-apt-repository
sudo apt-get install dpkg-dev
sudo mkdir -p /usr/local/mydebs

#! /bin/bash
 cd /usr/local/mydebs
 dpkg-scanpackages . /dev/null | gzip -9c > Packages.gz
 (write in chmod u+x ~/bin/update-mydebs
 
 Sources.list

add the line

deb file:/usr/local/mydebs ./

)

sudo update-mydebs
sudo apt-get update

https://wiki.debian.org/MaintainerScripts


