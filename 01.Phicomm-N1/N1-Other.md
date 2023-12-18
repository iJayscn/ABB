/dev/mmcblk2


sudo mount /dev/mmcblk2p2 /mnts/emmc ext4 defaults 0 0

mount /dev/mmcblk2p1 /mnts/emmcp1



auto eth0
iface eth0 inet manual
up ip link set eth0 promisc on
auto macvlan
iface macvlan inet static
   address 192.168.1.XXX
   netmask 255.255.255.0
   gateway 192.168.1.XXX
   dns-nameservers 192.168.1.XXX
   pre-up ip link add macvlan link eth0 type macvlan mode bridge
   post-down ip link del macvlan link eth0 type macvlan mode bridge
   
   
   
