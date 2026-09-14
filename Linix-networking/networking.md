1. waht is lo interface in linux ?

A. lo stands for loopback.It is a virtual network interface that allows a Linux machine to communicate with itself.
   The most common loopback IPv4 address is: 127.0.0.1/8
   To check this use CMD: ip addr show <interface_name>, ip addr, ip link 
   In loopbackip we have 127.0.0.1/8, */8* means: The /8 means the loopback IPv4 range is: *127.0.0.0 -> 127.255.255.255*
   


2. What is eth0 ?
A. eth0 is the network connection that Linux uses to communicate with other machines.
   eth0: usually represents an Ethernet network interface.
   It may be: a physical network card, a virtual network interface, a virtual NIC provided by a cloud platform
   eth0:
    inet 192.168.1.100/24
   eth0 has a MAC address:  link/ether 00:11:22:33:44:55 --> looks similar
   eth0 contains both MAC address and IP address
   eth0 is the interface, and an IP address can be assigned to that interface.
   Similarly, Linux can have multiple network interfaces: lo, eth0, eth1, docker0..etc. Each interface can have a different purpose.
   So eth0 is the interface through which the traffic leaves the Linux machine.
   
Note:

          In both lo and eth0 interfaces you will commenly see UP and LOWER_UP means the interface is enabled and the underlying link is operational.
          UP:  means the network interface is administratively enabled.
          CMD: 
               sudo ip link set eth0 up OR down

          LOWER_UP: It means the lower layer/link underneath the interface is detected as operational.
          UP + LOWER_UP: Linux has enabled the interface and the link is operational.
          

