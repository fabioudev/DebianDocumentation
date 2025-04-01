# DebianDocumentation
## This is a little Guide to make an ssh connectin via port forwarding with 2 Debain Servers
> Info: Server 1 = forwarding-Server, Server 2 = target-Server
# Guide:
# Everything on Server 1 :
## 1. Install dependencies:
make shure you habve installed ``iptables``, ``iptables-persistent``
````Install iptables
sudo apt install iptables -y
````
````install iptables-persistent
sudo apt install Iptables-persistent -y
````
**Info**: ``iptables-persistent`` is to save your iptables, so they stay after you reboot. And ``iptables-persistent`` doesn`t wotk with ``ufw`` so you habe to block ports with ``iptables``.

## 2. set the IpTables
### To catch a port and forward it you have to use :
````
iptables -t nat -A PREROUTING -p tcp --dport <port that should be catched> -j DNAT --to-destination <Your Server 2 IP-Adress with target port>
````
### To accept forwarded packages in iptables use :
````
iptables -A FORWARD -p tcp -d <Your Server 2 IP-Adress> --dport <target port> -j ACCEPT
````
### To give the packages that go out the IP-Adress from Server 2 use :
````
iptables -t nat -A POSTROUTING -o <Your internet interface (normally eth0)> -j MASQUERADE
````
#### Filled with example data :
````
iptables -t nat -A PREROUTING -p tcp --dport 2222 -j DNAT --to-destination 104.56.203.89:22
````
````
iptables -A FORWARD -p tcp -d 104.56.203.89> --dport 22 -j ACCEPT
````
````
iptables -t nat -A POSTROUTING -o eth0 -j MASQUERADE
````

## 3. save IP-tables to stay after rebooting
### Everytime you make new IP-tables and want to save them you have to make :
````
sudo netfilter-persistent save
````
## 4. make sure that the ports that you use arent blocked by any firewalls
### At least you have to make sure that the ports that you use arent used by other applicaitons and dont get blocked by firewalls from you or your provider.

# Everything on Server-2 :
## On Server-2 you just have to make sure that the port you want to use is open, and that its configured for ssh.
## 1. make shure ufw is installed
````
sudo apt install ufw -y
````
**Info :** make sure that you dont enable ufw before you havent allowed your ssh port so you still can make an ssh connection.
### Set yout firewall settings :
````Allow a port with just from a specific IP-adress
sudo ufw allow from 104.56.203.89 to any port 22
````
````Allow a port from any IP-adress
sudo ufw allow 22
````
````To check your ufw settings
sudo ufw status verbose
````
### At least enable ufw but make sure that your ssh port is allowed :
````
sudo ufw enable
````
### (Optional) make custom ssh ports
````edit the /etc/ssh/sshd_config
sudo nano /etc/ssh/sshd_config
````
#### Uncomment port then add a port or edit the port for Example
````
Port 23
Port 24
````
