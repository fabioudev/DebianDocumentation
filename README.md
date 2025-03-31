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
