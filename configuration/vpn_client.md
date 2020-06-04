# Virtual Private Network (VPN) Client

How you set up a VPN client on your Raspberry Pi will depend on which protocol the remote VPN you're trying to connect to uses.

## Connecting using OpenVPN

The OpenVPN protocol is one of the youngest VPN protocols; it is open-source and generally considered one of the more secure protocols.

To get OpenVPN working on Raspberry Pi, first you’ll need to install the OpenVPN package:

```
$ sudo apt-get install openvpn
```

Then you’ll need to create a `client.conf` configuration file in `/etc/openvpn`.

```
client
dev tun

proto udp
remote HOSTNAME PORT

resolv-retry infinite
nobind

user nobody
group nogroup

persist-key
persist-tun

# Path to your certificates (ca.crt, client.crt, and client.key)
ca FILEPATH_CA
cert FILEPATH_CRT
key FILEPATH_KEY

ns-cert-type server
comp-lzo
verb 3
```

Replace `HOSTNAME` and `PORT` with the name and port number of your remote VPN server, and `FILEPATH_CA`, `FILEPATH_CRT`, and `FILEPATH_KEY` with the fully resolved path to your `ca.rt`, `client.crt`, and `client.key` certificate files; you can obtain these files from your VPN provider. While these files normally live alongside your configuration file in `/etc/openvpn`, it isn’t necessary to have them saved there if you really want them to live somewhere else.

Then in a terminal window, type:

```
$ sudo openvpn /etc/openvpn/client.conf
```

You should then be connected to the remote VPN via OpenVPN.

## Connecting using PPTP

The Point Tunneling Protocol (PPTP) is an older VPN protocols, and despite known vulnerabilities, it is still one of the more widely used ones because it’s integrated into Windows, macOS, and Linux. If your remote VPN service offers other protocols, such as OpenVPN, I suggest that you choose one of these instead, since they are more secure. However, if you do need to use PPTP, you can do so on Raspberry Pi.

First you’ll need to install the PPTP package:

```
$ sudo apt-get install pptp-linux
```

Then go ahead and create a `/etc/ppp/peers/pptp-config` file to hold your PPTP configuration, and copy and paste the following into the file:

```
pty "pptp HOSTNAME --nolaunchpppd --debug"
name USERNAME
password PASSWORD
remotename PPTP
require-mppe-128
require-mschap-v2
refuse-eap
refuse-pap
refuse-chap
refuse-mschap
noauth
debug
persist
maxfail 0
defaultroute
replacedefaultroute
usepeerdns
```

Replace `HOSTNAME`, `USERNAME`, and `PASSWORD` with the name of the remote VPN server, and your own username, and password fro the server.

Then in a terminal window, type:

```
$ sudo pon /etc/ppp/peers/pptp-config
```

You should then be connected to the remote VPN via PPTP.

