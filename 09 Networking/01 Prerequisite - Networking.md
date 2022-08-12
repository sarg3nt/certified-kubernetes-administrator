# Prerequisite - Networking 

## Basic IP and Route Commands
```bash
# To see interfaces on a host
ip link

# To add an IP address to a link temporarily (will not survive reboot)
# where dev means device and eth0 is the device to add the address to
ip addr add 192.168.0.1/24 dev etho0
# Permanently
sudo ifconfig eth0 192.168.0.1 netmask 255.255.255.0”

# To see the routes on the system (route is part of net-tools)
route
# or
ip route 

# To add a new route (gateway) from our current network of 192.168.1.0/24 to 192.168.2.0/24.
# What network are we routing to and via what router (gateway)
ip route add 192.168.2.0/24 via 192.168.1.1
# Now add the reciprocal route to the machine with an IP in the 192.168.2.0/24 network
ip route add 192.168.1.0/24 via 192.168.2.1
# To add a default route for any network the machine can't already talk to
# default can also be 0.0.0.0
ip route add default via 192.168.2.1
# Often a default route is all you need unless your network has different gateways for different network, then you need an entry for each.

# To see what ports are open and how many connections they have do 
netstat -plnt
```

## Setting up a Linux Host as a Router

Assume we have three systems, A, B and C where A is on network 192.168.1.x and C is on 2.x and B has two interfaces, eth0 and eth1 where eth0 has an ip on 1.x and eth1 has an ip on 2.x.  
If we add routes from A to B and C to B then B can act as a router except that Linux does not by default route traffic between two different physical interfaces.  

We can change this by editing `/proc/sys/net/ipv4/ip_forward` which defaults to 0.  To route the traffic, change this to a 1 `echo 1 > /proc/sys/net/ipv4/ip_forward`.  

Editing `/proc/sys/*` does not persist changes across reboot, to make this change permanent we must edit `/etc/sysctl.conf` and change `net.ipv4.ip_forward = 1`

## DNS

### Hosts File

The hosts file is located at `/etc/hosts`  
Add an entry to give another system a name.
```bash
echo "192.168.1.11      db" > /etc/hosts
```

### Setting a DNS Server

The `/etc/resolv.conf` file should contain an entry to the DNS server that looks like this `nameserver 192.168.1.100`  
You can have as many `nameserver` lines as needed, private DNS server first then public after that with backups after that, etc.  

You can also configure your private DNS server with forward any unknown requests to a public DNS server.  

### Hosts / DNS Search Order

By default the system looks at `/etc/hosts` first and if it finds an entry there it uses it, if not then it checks the nameserver specified in `/etc/resolv.conf`, however, the order can be changed by editing the `hosts` line in `/etc/nsswitch.conf`.  Default is `hosts:  files dns`, switching the order to `hosts:  dns files` will switch the order they are checked in.

### Adding a Default Domain

Add a `search` entry in `/etc/resolv.conf` with your companies domain to enable short name resolution.   

Example:  If you had a web server at `web.mycompany.com` but wanted to be able to get to it internally with just `web` then add `search mycompany.com` to the `resolv.conf` file.  This entry is a space separated list of of domains to search: `search mycompany.com pro.ycompany.com`

### Record Types

| Type | Name | Destination |
| ----------- | ----------- | ---------|
| A | web-server | 192.168.1.1 |
| AAAA | web-server | 2001:0db8:85a3:0000:0000:8a2e:0370:7334 |
| CNAME | food.web-server | eat.web-server, hungry.web-server |

### Tools

```bash
# Ping
ping 192.168.0.1

# nslookup, does not look in hosts file, is designed to talk to DNS servers
nslookup www.google.com

# dig, returns more details
dig www.google.com

```