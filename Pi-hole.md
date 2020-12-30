#Simple-ish Raspberry Pi-hole Setup Guide
By Antony Rivera


##Pre-requisites
* Network connected Raspberry Pi 2 Model B or better running Raspberry Pi OS or similar
* Static IP for the Pi-hole
* Windows Desktop or similar
* PuTTY or a HDMI compatible monitor with mouse and keyboard to interact with the Pi
* Necessary cables and power supplies


##Setup Pi-hole
###Connecting to the Raspberry Pi
1. Turn on your Raspberry Pi
2. Connect to the Raspberry Pi via PuTTY or via a HDMI compatible monitor and USB mouse and keyboard
   1. If using a HDMI monitor with a mouse and keyboard, open a terminal using the desktop terminal shortcut or CTRL+ALT+F1 key combination
3. Login if not already logged in

###Updating Raspberry Pi
Source: https://www.raspberrypi.org/documentation/raspbian/updating.md
4. Run the following command to update the system packages:
`sudo apt update`
5. Run the following command to update installed packages:
`sudo apt full-upgrade`
_Note: Consider creating a job to perform updates automatically._
6. Complete any prompts to have your OS and packages be current

###Updating the Pi-hole Server Firewall
Source: https://docs.pi-hole.net/main/prerequisites/
7. Run the following commands to update the Pi-hole’s firewall with the required rules:
`iptables -I INPUT 1 -s 192.168.0.0/16 -p tcp -m tcp --dport 80 -j ACCEPT
iptables -I INPUT 1 -s 127.0.0.0/8 -p tcp -m tcp --dport 53 -j ACCEPT
iptables -I INPUT 1 -s 127.0.0.0/8 -p udp -m udp --dport 53 -j ACCEPT
iptables -I INPUT 1 -s 192.168.0.0/16 -p tcp -m tcp --dport 53 -j ACCEPT
iptables -I INPUT 1 -s 192.168.0.0/16 -p udp -m udp --dport 53 -j ACCEPT
iptables -I INPUT 1 -p udp --dport 67:68 --sport 67:68 -j ACCEPT
iptables -I INPUT 1 -p tcp -m tcp --dport 4711 -i lo -j ACCEPT
iptables -I INPUT -m conntrack --ctstate RELATED,ESTABLISHED -j ACCEPT`
_Note: Consider appending a drop all rule to ensure only whitelisted traffic is allowed post-setup of Pi-hole. Don’t forget to whitelist your managing device if performing the setup remotely._

###Install Pi-hole
Source: https://docs.pi-hole.net/main/basic-install/
8. Run the following command to download and execute the Pi-hole installer:
`curl -sSL https://install.pi-hole.net | bash`
9. Use the static IP assigned to the Pi-hole when prompted
_Note: When prompted for backup DNS servers, I suggest using Quad9 or Cloudflare as their DNS servers actively filter malicious domains._
10. Complete any prompts to install Pi-hole
_Note: Consider creating a job to perform Pi-hole updates automatically via the command: pihole -up_


##Configuring the Pi-hole
11. Login to the administrator web GUI via the following URL:
`http://**<Pi-hole Static IP>**/admin/index.php?login`
Replace <Pi-hole Static IP> with the IP of your Pi-hole
12. Navigate to the Adlists section of the Pi-hole (Group Management > Adlists):
`http://**<Pi-hole Static IP>**/admin/groups-adlists.php`
13. Add curated domain blacklists to the adlist - see https://firebog.net/ for suggestions
14. Update Adlists (Tools > Update Gravity):
`http://**<Pi-hole Static IP>**/admin/gravity.php`
_Note: Consider creating a job to perform Adlist updates automatically via the command: pihole updateGravity_


##Configuring DNS for Endpoints or Clients
15. Now that the Pi-hole is set up and configured, it now needs to be used. Point your router, DHCP server, or device to the Pi-hole to use the blacklisting capabilities. (See Google for guides on configuring your specific device or setup.) DNS queries can also be forwarded to the Pi-hole in conjunction with an existing DNS server.
