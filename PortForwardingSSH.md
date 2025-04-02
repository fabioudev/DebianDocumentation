# PortForwardingSSH
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
## Here is a little visualisation for the port forwarding
https://viewer.diagrams.net/?tags=%7B%7D&lightbox=1&highlight=0000ff&edit=_blank&layers=1&nav=1&title=ServerVisualization.drawio&dark=auto#R%3Cmxfile%3E%3Cdiagram%20name%3D%22Seite-1%22%20id%3D%225D0RD7XVquBDqaYYt4bj%22%3E7Vlbb9owFP41SNsDKLETCI9c2k6qJk1D08rTlCZuYimJU8fc9ut3TJyrScs6oFTaC9jHx8f2d774fIQensXbO%2B6m4Vfmk6iHDH%2Fbw%2FMeQshwHPiSll1uMbGFckvAqa9slWFBfxNlNJR1RX2SNRwFY5GgadPosSQhnmjYXM7Zpun2xKLmqqkbEM2w8NxIt%2F6kvghzq4NGlf0LoUFYrGwOx%2FlI7BbO6iRZ6PpsUzPhmx6eccZE3oq3MxJJ9Apc8nm3HaPlxjhJxDETyPds%2FjxfLqez%2B%2FDe2a1vJ7%2Fu%2BirK2o1W6sALwteEg30YQdipT9fQDGTz04ZQQXgEn6sk%2BFx4wIo1J3VWsSsA5GyV%2BETuwYDhTQghFqnrydENcAZsoYgj6JnlbFhfkG3nMc0SPKAdYTERfAcuBecKvBXjSiZtqvSZhU9YS91Q2VzFmKAMXYEKDYXrX2CMOjBG746Vha8NK0vD6kdGuAYURIILgFwEJLOFkXEkRs65MLI1jOzByB4gGw%2FMEdawgpOLJihuRIME2h4gAODiqcSHwp03UQMx9f2oC90mSU%2FxxFpNhE1HR9g5ADA%2BF8BDDWBrPDDRAGFrYNrOx0PYsuxXER5fEuGxhnDKuNjXZ75xuQ%2FVxI0lFMljlu4x0KoRAqYb%2FdJRFR%2BjmSk8kescVaeuPonaRTQ6cBFZB7JonyuLpp7GuDfDvenQCwWR8FIvlFKIytT6JIbPsvRJMB4pQJR8vFQ0nyaMDyQCX%2FJxQnrZ1Etm4k%2BkHpYoRm4GqQEsMuFyoZtryDexI1sqHtSIbC%2BlfWCr3nxbc5vvik4CR3yoHGV3WcSQnWravrdr5In4mj5vZQlOyVbcI0cQFU4bEPHavaSnvZZY%2B4UHjJPIFXTd3O%2BhZKsVvjGaiJrQGDUfcDxu8SU%2Fp5pVV%2FqtQG0JjK1WoBwHLdCee%2BWx%2F4GOukI5Lx0rCi5r5LxaOh7LRvSebMQtVYbRG9lojV8J1MFG4IG7q7ml0iHr3rDVseGK3HnE01Jd14qF3nhi%2B91VnB8%2Br1gx0M%2F2bzom4GAa6TaXOGq8kCgNVQ9KBteUTB78XaQMZwJ4xeSsvnmigmrardwdKKkXFahF4HPk9UWJeh2JPVFatTvkwPuFy6ZVf991pspUVpl6jamVnI4q8xaBdcLKZB1ZmTrSfpnKhNo66a2VCbd%2FUbVf2ZxZJ2H0n40vyvaPoJNOx8b2i%2BvRqdgI3epvhty9%2BrcG3%2FwB%3C%2Fdiagram%3E%3C%2Fmxfile%3E
