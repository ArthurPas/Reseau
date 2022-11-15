### CISSOKHO Issiaka PASCAL Arthur
# DNS or how to give a name to an IP
### Presentation
TODO
### IP and name configuration
To begin with, we have to configurate client 1, client2, DNS1 and DNS2 to allow them to have IP addresses from the DHCP server on root. For that we have to write on etc/network/interface file the following line :
>iface eth0 inet dhcp

Our machines have this IP :

>Client1: 192.168.0.10

>Client2: 192.168.0.20

>dns1: 192.168.0.1

>dns2: 192.168.0.2

The routing table is the folowing : TODO:inserer image route.png

We can see that the gateway have the IP 192.168.0.254

Now, we edit the etc/hosts file of dns1 to give a name to the IP adresses associated with the machines. 

<blockquote>192.168.0.10   client1 
192.168.0.20   client2
192.168.0.2    dns2
</blockquote>
 
We can see on the resolv.conf file that the "nameserver" IP is 172.16.0.3. This IP is given by the DHCP server "root".

Now we edit the /etc/dhcp/dhclient.conf file with for the DNS machine:

>supersede domain-name-servers 127.0.0.1;

and for the clients :

>supersede domain-name-servers < ip of the DNS server associated >

We can see that when we ifdown the interface eth0 on client and then ifup the resolv.conf file changes :

TODO Inserer resolv.conf.png

Then, we edit the /etc/bind/named.conf.options on dns1 and dns2 to allow external DNS requests.

### DNS servers configuration

To begin with, we edit the /etc/bind/named.conf.local file with 
<blockquote>
zone "netas"{    

type master;

file "/etc/bind/db.netas";

};
</blockquote>
With that the dns1 will be in charge of the main domain "netas".

Then we have to copy the the /etc/bind/db.empty file in /etc/bind/db.netas

After that, we can we have to update the /etc/bind/db.netas file with this entry :

TODO Inserer db.netasModif.png

So, now we have the aliases for the dns1 and dns2 machine with this names and dns-primaire, dns-secondaire name too

Don't forget to update the serial number

We can see the changes by realoading or restarting the dns and now we can ping from client1 and client2 as we can see :

TODO Inserer pingVersLesServeursDns.png

Now we edit the /etc/bind/named.conf.local on the dns2 to declare the netas zone as secondary from the server dns1.

Now we can see that all works well with the command "host" from client2 :

TODO inserer resultatHostDepuisClient2.png  

The mask /22 means that there are 1024 addresses possible and have the subnet mask 255.255.252.0
