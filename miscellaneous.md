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


e.g.:

