# Prerequisite - Network Namespaces

## Process Namespaces

A process namespace is created around a container so it cannot see other processes, but the system can see it.

## Network Namespaces

A network namespace surrounds a container so it has no visibility to any network related information on the host.  
Inside the container it has its own virtual interfaces, routing and ARP tables.

To create a new network namespace on a Linux host:  
```bash 
# Add a new namespace
ip netns add <name>
ip netns add red
ip netns add blue

# llist namespaces
ip netns

# Run standard IP tooling from within the namespace
ip netns exec red ip link
# or, some commands support namespaces natively 
ip -n red link

# Add a pipe / Virtual network cable between the two namespaces red and blue
# Create the pipe
ip link add veth-red type veth peer name veth-blue
# Assign the two ends to each namespace
ip link set veth-red netns red
ip link set veth-blue netns blue
# Add IPs to the namespaces
ip -n red addr 192.168.15.1 dev veth-red
ip -n blue addr 192.198.15.2 dev veth-blue
# Bring up the interface
ip -n red link set veth-red up
ip -n blue link set veth-blue up

# To delete the red and blue links
ip -n red link del veth-red
# The other end gets deleted automatically

# The above works ok for a small number of namepsaces but a virtual switch works better.  
# There are a few solutions, Linux Bridge, Open vSwtich, etc.  We will use Linux Bridge
ip link add v-net-0 type bridge
ip link set dev v-net-0 up

# now create new "cables" that we can use to link to the bridge
ip link add veth-red type veth peer name veth-red-br
ip link add veth-blue type veth peer name veth-blue-br

# Connect them to the interfaces
ip link set veth-red netns red
# Link the other end to the bridge v-net-0
ip link set veth-red-br master v-net-0
# and now the blue interface
ip link set veth-blue netns blue
ip link set veth-blue-br master v-net-0
# Add Ips and turn them up
ip -n red addr add 192.168.15.1 dev veth-red
ip -n blue addr add 192.168.15.2 dev veth-blue
ip -n red link set veth-red up
ip -n blue link set veth-blue up

# The above can now ping each other form one namespace to another but the host can't talk to them.  
# To grant the host access we need to add an IP to the bridge v-net-0
ip addr add 192.168.15.5/24 dev v-net-0

# The namespaces cannot be accessed  from the outside world and the world can't access them.
# We need to add a route to the host which will act as our router
# Here we are trying to reach 192.168.1.0/24 and 15.5 is the v-net-0
ip nets exec blue ip route add 192.168.1.0/24 via 192.168.15.5

# We still can't reach the outside world because we don't have a NAT (network address translation) service
iptables -t nat -A POSTROUTING -s 192.168.15.0/24 -j MASQUERADE

# Now we want to reach the internet so we need to add a default gateway which will be the v-eth-0 -->  192.168.15.5
ip netns exec blue ip route add default via 192.168.15.5

# To get incoming traffic from the outside world to connect to a namespace use a nat entry  Here 192.168.15.2 is the IP of one of the namespaces
iptables -t nat -A PREROUTING --dport 80 --to-destination 192.168.15.2:80 -j DNAT
```

## FAQ

While testing the Network Namespaces, if you come across issues where you can't ping one namespace from the other, make sure you set the NETMASK while setting IP Address. ie: 192.168.1.10/24

`ip -n red addr add 192.168.1.10/24 dev veth-red`

Another thing to check is FirewallD/IP Table rules. Either add rules to IP Tables to allow traffic from one namespace to another. Or disable IP Tables all together (Only in a learning environment).