# DebianDocumentation
## This is a little Guide to make an ssh connectin via port forwarding with 2 Debain Servers
# Guide:
## 1. Install dependencies:
make shure you habve installed ``iptables``, ``iptables-persistent``
````Install iptables
sudo apt install iptables
````
````install iptables-persistent
sudo apt install Iptables-persistent
````
**Info**: ``iptables-persistent`` is to save your iptables, so they stay after you reboot.

## 2. set the IpTables
``iptables -t nat -A PREROUTING -p tcp --dport 2222 -j DNAT --to-destination 49.12.234.158:22``
``iptables -A FORWARD -p tcp -d 49.12.234.158 --dport 22 -j ACCEPT``
``iptables -t nat -A POSTROUTING -o eth0 -j MASQUERADE``
