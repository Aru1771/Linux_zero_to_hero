Linux Networking Learning Path
==================================


                      LINUX NETWORKING
                           |
          +----------------+----------------+
          |                                 |
     BASIC CONCEPTS                    LINUX CONFIG
          |                                 |
          v                                 v
     IP Address                         ip command
     MAC Address                        Network interface
     Subnet                             Routes
     Gateway                            ARP
     DNS                               DNS config
     Ports                              Firewall
     TCP/UDP
          |
          v
     PACKET FLOW
          |
          v
     veth pairs
     bridges
     namespaces
     routing
     NAT
     iptables/nftables
          |
          v
     KUBERNETES NETWORKING
          |
          v
       CNI


I would learn it as a hands-on course, not just theory.


I would learn it as a hands-on course, not just theory.

Part 1 — Understand the Linux Network Interface
--------------------------------------------------
Start with:

ip link

Example:

1: lo: <LOOPBACK,UP,LOWER_UP>
2: eth0: <BROADCAST,MULTICAST,UP,LOWER_UP>

Here:

lo

is the loopback interface.

eth0

is the network interface connected to the network.

Check details:

ip addr

Example:

2: eth0:
    inet 192.168.1.100/24
    inet6 fe80::1234/64

The important part:

192.168.1.100/24

means:

IP address = 192.168.1.100
Prefix      = /24

Part 2 — Understand IP Address + Subnet
-----------------------------------------
You absolutely need to understand this before Kubernetes networking.

For:

192.168.1.100/24

the /24 means:

Network = 192.168.1.0
Hosts   = 192.168.1.1 - 192.168.1.254
Broadcast = 192.168.1.255

Conceptually:

192.168.1.0/24
│
├── Network
│
├── 192.168.1.1
├── 192.168.1.2
├── ...
├── 192.168.1.100
├── ...
├── 192.168.1.254
│
└── Broadcast

Later, this becomes extremely important when you see:

Pod CIDR
Node CIDR
Service CIDR
VPC CIDR
Subnet
Part 3 — Understand MAC Address

Every Ethernet interface has a MAC address.

Run:

ip link show eth0

Example:

link/ether 00:11:22:33:44:55

So you have:

IP address
    ↓
192.168.1.100

MAC address
    ↓
00:11:22:33:44:55

You need to understand why both exist.

The simplified concept is:

Application
     ↓
IP packet
     ↓
Ethernet frame
     ↓
MAC address
     ↓
Network interface
Part 4 — ARP

Now ask:

If I know the destination IP, how does Linux find the destination MAC address on the local network?

ARP.

Check ARP/neighbour information:

ip neigh

Example:

192.168.1.1 dev eth0 lladdr aa:bb:cc:dd:ee:ff REACHABLE

Meaning:

Destination IP
192.168.1.1

        ↓ ARP

Destination MAC
aa:bb:cc:dd:ee:ff

This is very important for understanding packet delivery.

Part 5 — Linux Routing

This is probably the most important Linux networking topic for Kubernetes.

Run:

ip route

Example:

default via 192.168.1.1 dev eth0
192.168.1.0/24 dev eth0 proto kernel scope link src 192.168.1.100

Understand every piece.

Route 1
192.168.1.0/24 dev eth0

means:

To reach the 192.168.1.0/24 network, use eth0.

Route 2
default via 192.168.1.1 dev eth0

means:

If Linux doesn't have a more specific route, send the packet to 192.168.1.1.

That's your default gateway.

Part 6 — Learn the Linux Routing Decision

Suppose:

Linux server
192.168.1.100

wants to communicate with:

192.168.1.200

Linux checks its routing table.

Destination:
192.168.1.200
       |
       v
Routing table
       |
       v
192.168.1.0/24 dev eth0
       |
       v
eth0

But suppose the destination is:

10.10.10.20

Then:

10.10.10.20
      |
      v
No specific route
      |
      v
default route
      |
      v
192.168.1.1
      |
      v
eth0

You can test Linux's routing decision directly:

ip route get 10.10.10.20

This command is extremely useful.

Part 7 — Configure an IP Address

Temporarily configure an IP:

sudo ip addr add 192.168.10.100/24 dev eth0

Check:

ip addr show eth0

Remove it:

sudo ip addr del 192.168.10.100/24 dev eth0

Bring interface up:

sudo ip link set eth0 up

Bring it down:

sudo ip link set eth0 down
Part 8 — Configure Routes

Add route:

sudo ip route add 10.10.20.0/24 via 192.168.1.1

Now:

10.10.20.0/24
       |
       v
192.168.1.1
       |
       v
eth0

Check:

ip route

Delete:

sudo ip route del 10.10.20.0/24

This is directly relevant to Calico.

When you eventually see something like:

10.244.2.0/24 via 192.168.1.20

you'll immediately understand:

"Traffic for the Pod network 10.244.2.0/24 should go through node 192.168.1.20."

Part 9 — Understand DNS

Check DNS:

cat /etc/resolv.conf

You may see:

nameserver 8.8.8.8

Test DNS:

nslookup google.com

or:

dig google.com

Understand:

Application
     |
     | google.com
     v
DNS resolver
     |
     v
IP address
     |
     v
Network connection

Later in Kubernetes you'll have:

pod
 |
 v
CoreDNS
 |
 v
Service IP

So Linux DNS knowledge is essential.

Part 10 — TCP and UDP

You should understand:

IP
 ↓
TCP / UDP
 ↓
Port
 ↓
Application

For example:

10.10.1.20:443

means:

IP   = 10.10.1.20
Port = 443
Protocol = TCP

Check listening ports:

ss -lntup

Example:

LISTEN 0 128 0.0.0.0:22
LISTEN 0 128 0.0.0.0:80

Understand the difference between:

0.0.0.0:80
127.0.0.1:80
192.168.1.100:80

This becomes very important when troubleshooting Kubernetes applications.

Part 11 — Network Namespaces

This is where your Kubernetes networking learning really begins.

Linux supports network namespaces.

Each namespace can have its own:

Network interfaces
IP addresses
Routing table
ARP table
iptables rules

Conceptually:

Host
+--------------------------------+
|                                |
| Network namespace              |
|                                |
| eth0                           |
| 10.10.1.10                     |
|                                |
| routing table                  |
|                                |
+--------------------------------+

Kubernetes uses Linux network namespaces for Pods.

Part 12 — veth Pair

Now you'll understand one of the most important concepts for Calico.

A veth pair is like a virtual network cable with two ends.

        veth pair
     +-------------+
     |             |
     v             v
  Namespace      Host
    eth0       caliXXXX
     |             |
     |             |
   Pod           Node

Imagine physically connecting two computers with an Ethernet cable.

A veth pair does something similar virtually.

Pod namespace
      |
    eth0
      |
      | virtual cable
      |
  cali12345
      |
Linux host

When you understand this, Calico becomes much easier.

Part 13 — Linux Bridge

Then learn bridges.

A Linux bridge behaves somewhat like a virtual Layer-2 switch.

                 Linux Bridge
                     |
        +------------+------------+
        |            |            |
       veth         veth         eth0
        |            |             |
       Pod          Pod         Network

You'll encounter bridges in:

Docker
containerd networking
Kubernetes
virtual machines
Linux networking
Part 14 — NAT

Understand:

SNAT
DNAT
MASQUERADE

Example:

Private network

192.168.1.100
      |
      v
    NAT
      |
      v
Public IP
203.x.x.x

Useful commands/concepts:

iptables
nft
conntrack

Don't start with memorizing commands.

First understand why NAT exists and what changes in the packet.

Part 15 — Firewall

Then learn:

iptables
nftables

Understand:

INPUT
OUTPUT
FORWARD

For example:

Internet
   |
   v
eth0
   |
   v
INPUT
   |
   v
Application

And:

Pod
 |
 v
FORWARD
 |
 v
Another interface

That FORWARD concept becomes very important when troubleshooting Kubernetes nodes.

Part 16 — Packet Flow

Eventually you should be able to look at:

curl http://10.10.2.20:8080

and mentally understand:

Application
    |
    v
Socket
    |
    v
TCP
    |
    v
IP
    |
    v
Routing table
    |
    v
Network interface
    |
    v
Ethernet
    |
    v
Network

And on the receiving side:

Network
   |
   v
NIC
   |
   v
IP
   |
   v
TCP
   |
   v
Socket
   |
   v
Application
Part 17 — Troubleshooting Tools

You should become comfortable with these:

Interface
ip link
ip addr
Routing
ip route
ip route get <destination>
ARP/neighbours
ip neigh
Ports
ss -lntup
Connectivity
ping
curl
nc
DNS
dig
nslookup
Packet capture
tcpdump
Firewall
iptables
nft
Processes
ps
lsof
The sequence I recommend for YOU

Because you're targeting a DevOps/Kubernetes role, don't spend weeks studying every Linux networking feature.

Follow this:

DAY 1
│
├── IP addresses
├── CIDR
├── subnet
├── gateway
└── MAC address

DAY 2
│
├── ip link
├── ip addr
├── ip route
├── ip route get
└── ip neigh

DAY 3
│
├── TCP
├── UDP
├── ports
├── sockets
├── ss
└── DNS

DAY 4
│
├── Network namespaces
├── veth pairs
├── Linux bridge
└── packet flow

DAY 5
│
├── iptables
├── nftables
├── INPUT
├── OUTPUT
├── FORWARD
└── NAT

DAY 6
│
├── tcpdump
├── troubleshooting
├── routing problems
├── DNS problems
└── connectivity problems

DAY 7
│
└── Kubernetes networking
       |
       ├── Pod network
       ├── Service network
       ├── kube-proxy
       ├── CNI
       ├── veth
       ├── routing
       └── Calico
Most important point

Don't just memorize:

ip addr
ip route
ip link

Understand what Linux is doing.

For example, eventually you should be able to explain this:

ip route

output:

default via 192.168.1.1 dev eth0
10.244.1.0/24 dev eth0
10.244.2.0/24 via 192.168.1.20 dev eth0

as:

"Linux sends normal external traffic through the default gateway. The 10.244.1.0/24 network is directly reachable through eth0, while traffic destined for 10.244.2.0/24 must be forwarded through the node at 192.168.1.20."

          |
          v
       CALICO
