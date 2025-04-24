# MyOpenWrt

Sunday April 13th 2025:
/etc/init.d/openvpn status shows inactive
To turn on the location for edinburgh:
- connect to openwrt network
- Check network -> location to be openwrt
- Check DNS address is 192.168.66.1
- ssh root@192.168.66.1
- /etc/init.d/openvpn status -> shows inactive
- /etc/init.d/openvpn start
- /etc/init.d/openvpn status -> shows running
- LuCI: http://192.168.66.1/ -> VPN -> EdinburghTest1 is started





Adguard Dashboard:
http://192.168.66.1:3002/



cat /etc/openvpn/EdinburghTest1.ovpn 
client
dev tun
proto udp
remote uk-edi.prod.surfshark.com 1194
remote-random
nobind
tun-mtu 1500
mssfix 1450
ping 15
ping-restart 0
reneg-sec 0
remote-cert-tls server
auth-user-pass /etc/openvpn/auth.txt
verb 6
cipher AES-256-CBC
auth SHA512

<ca>
-----BEGIN CERTIFICATE-----
...
-----END CERTIFICATE-----
</ca>
key-direction 1
<tls-auth>
#
# 2048 bit OpenVPN static key
#
-----BEGIN OpenVPN Static key V1-----
...
-----END OpenVPN Static key V1-----
</tls-auth>

# Prevent the VPN from overriding routes and DNS
redirect-gateway def1
# route-nopull
# pull-filter ignore "dhcp-option DNS"
push "dhcp-option DNS 192.168.66.1"



cat /etc/config/openvpn
config openvpn 'custom_config'
	option config '/etc/openvpn/my-vpn.conf'

config openvpn 'sample_server'
	option port '1194'
	option proto 'udp'
	option dev 'tun'
	option ca '/etc/openvpn/ca.crt'
	option cert '/etc/openvpn/server.crt'
	option key '/etc/openvpn/server.key'
	option dh '/etc/openvpn/dh2048.pem'
	option server '10.8.0.0 255.255.255.0'
	option ifconfig_pool_persist '/tmp/ipp.txt'
	option keepalive '10 120'
	option persist_key '1'
	option persist_tun '1'
	option user 'nobody'
	option status '/tmp/openvpn-status.log'
	option verb '3'

config openvpn 'sample_client'
	option client '1'
	option dev 'tun'
	option proto 'udp'
	list remote 'my_server_1 1194'
	option resolv_retry 'infinite'
	option nobind '1'
	option persist_key '1'
	option persist_tun '1'
	option user 'nobody'
	option ca '/etc/openvpn/ca.crt'
	option cert '/etc/openvpn/client.crt'
	option key '/etc/openvpn/client.key'
	option verb '3'

config openvpn 'EdinburghTest1'
        option enabled '1'
	option config '/etc/openvpn/EdinburghTest1.ovpn'


##### WireGuard VPN ####
https://docs.google.com/spreadsheets/d/1lidLRUfeKhcIxfMo9-MyWqeG8ImvjDEMeLwEnekhtrc/edit?gid=0#gid=0

Arris Surfboard Router:
Set up a service to translate UPD on port 51822 to server IP 192.168.0.249 (OpenWRT Router) 
Public IP of Surfboard Router: 24.6.41.59

OpenWRT Router Wireguard Settings:
OpenWrt	
Protocol	Wireguard VPN
Disable this Interface 	Unchecked
Bring Up on Boot	Checked
Private Key	#ERROR!
Public Key	8lWsJ2oUAGL0oWm8zO9Tg6MQaUKTyvphgDQ6HctuElA=
Generate new key pair	
Listen Port	51822
IP Addresses	10.14.0.1/24
No Host Rules	Unchecked
	
OpenWrt Peer	
Description	Tether
Public Key	/eagSpkV7pJH/Beg38ECSAYQWlLJhaRYye9CMJjQAls=
Private Key (optional)	Blank
Generate new key pair	
Preshared Key	Blank
Allowed IPs	10.14.0.2/32
Route Allowed IPs	Checked
Endpoint Host	Blank
Endpoint Port	Blank
Persistent Keep Alive	25

Firewall settings important VPN -> WAN? or LAN?

WireGuard App on Phone
WireGuard App	
Interface	
Name:	HomeVPN
Private Key	CCNyVCKQ1G2EP/NIt4xZxxg98pWzakAxKwUyjURTfUA=
Public Key:	/eagSpkV7pJH/Beg38ECSAYQWlLJhaRYye9CMJjQAls=
Addresses:	10.14.0.2/32
Listen port	Automatic
MTU	Automatic
DNS Servers:	10.14.0.1
	
Peer	
Public Key:	8lWsJ2oUAGL0oWm8zO9Tg6MQaUKTyvphgDQ6HctuElA=
Preshared Key	Blank
Endpoint	24.6.41.59:51822
Allowed IPs	0.0.0.0/0, ::/0
Presisten Keepalive	25


Testing if not working: 
Ilya Suggestions 4/22
(0) Repeat success on openwrt network with endpoint 192.168.66.1:51822
(1) Go on to Arris Surfboard Network with endpoint 192.168.0.249:51822 -> wirewall settings
(2) Tower Network with endpoint 24.6.41.59:51822 -> DMZ
 
