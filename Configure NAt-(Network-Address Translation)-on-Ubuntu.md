# Configure NAT(Network Address Translation) on Ubuntu:

**NAT or Network Address Translation** allows multiple computers on a private network to share a common IP to access the Internet. One set of IP addresses is used inside the organization and the other set is used by it to present itself to the internet.  This helps in conserving limited public IP space.

**VM1 (NAT Router):**

interfaces:

ens33 (public)            UNKNOWN        192.168.64.133/24 fe80::20c:29ff:fe78:fa85/64
ens37 (private)           UNKNOWN        10.10.10.2/24 fe80::20c:29ff:fe78:fa8f/64

VM2(Client):

ens33             UNKNOWN        10.10.10.3/24 fe80::20c:29ff:fe78:fa85/64 =⇒ gateway:10.10.10.2

VM1 configure:

open the sysctl.conf file and set the “net.ipv4.ip_forward” parameter to one by uncommenting it:

```bash
sudo vim /etc/sysctl.conf
```

Now enable the changes to above file using the command:

```bash
mrdex@srv1:~$ sudo sysctl -p
net.ipv4.ip_forward = 1
```

Now, install the iptables-persistent package:

```bash
sudo apt install iptables-persistent
```

List the already configured iptable policies by issuing the command:

```bash
sudo iptables --list
```

Now mask the requests from inside the LAN with the external IP of NAT router VM:

```bash
$ sudo iptables -t nat -A POSTROUTING -j MASQUERADE

$ sudo iptables --table nat --list

Chain PREROUTING (policy ACCEPT)
target     prot opt source               destination

Chain INPUT (policy ACCEPT)
target     prot opt source               destination

Chain OUTPUT (policy ACCEPT)
target     prot opt source               destination

Chain POSTROUTING (policy ACCEPT)
target     prot opt source               destination
MASQUERADE  all  --  anywhere             anywhere
```

Save the iptable rules using:

```bash
sudo sh -c "iptables-save > /etc/iptables/rules.v4"
```

Now, to check if everything is working fine, ping any public IP from the VM2:

![Screenshot 2024-04-04 135937.png](Configure%20NAT(Network%20Address%20Translation)%20on%20Ubun%20c79791e2a780414e920eabfcc3979b3d/Screenshot_2024-04-04_135937.png)