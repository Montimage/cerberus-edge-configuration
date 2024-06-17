

- MCC: 001
- MNC: 01
- MNC_LENGTH: 2
- TAC: 7
- KEY: 465B5CE8B199B49FAA5F0A2EE238A6BC
- OP: E8ED289DEBA952E4283B54E88E6183CA
- USIM Type: OP
- UE1: 001010000000001

# Setup SIM

- use [uicc](https://open-cells.com/index.php/uiccsim-programing/) program

```bash
cd hn/uicc-v3.2
sudo ./program_uicc --adm 12345678 --imsi 001010000000001 --isdn 00000001 --acc 0001 --key 465B5CE8B199B49FAA5F0A2EE238A6BC --xx E8ED289DEBA952E4283B54E88E6183CA -spn "MI" --authenticate
```

```bash
# Example
cd hn/uicc-v3.2
montimage@montimage-Precision-3570:~/hn/uicc-v3.2$ sudo ./program_uicc --adm 12345678 --imsi 001010000000003 --isdn 00000003 --acc 0001 --key 465B5CE8B199B49FAA5F0A2EE238A6BC --xx E8ED289DEBA952E4283B54E88E6183CA -spn "MI" --authenticate
Computed OPc from OP and Ki as: 2e001f1df0a0bb769940a2c6342cf795

Existing values in USIM
ICCID: 89860061100000000983
WARNING: iccid luhn encoding of last digit not done 
USIM IMSI: 001010000000003
USIM MSISDN: 00000003
USIM Service Provider Name: MI

Setting new values

Reading UICC values after uploading new values
ICCID: 89860061100000000983
WARNING: iccid luhn encoding of last digit not done 
USIM IMSI: 001010000000003
USIM MSISDN: 00000003
USIM Service Provider Name: MI
Succeeded to authentify with SQN: 160
set HSS SQN value as: 192
```


# Quectel RM502Q-AE
- 5G module optimized especially for eMBB & IoT applications
- band: [n1/n2/n3/n5/n7/n8/n12/n20/n25/n28/n38/n40/n41/n48/n66/n71/n77/n78/n79](https://www.quectel.com/news-and-pr/5g-rm502qae-earns-verizon-approval)
- Max. operating and storage temperature: -20°C to 70°C
- 5G up to 2.1 Gbps downlink/450 Mbps uplink — speed data will vary depending on the modem used

## Raspberry Pi

### Configuration
- Raspberry Pi 4, model B 2GB RAM
- 64-bit quad-core Cortex-A72 processor
- username: `montimage`
- hostname: `raspberry-pi`
- Also work on Raspberry Pi 3, model B 1GB RAM (32bit)
### Setup

- document: https://www.raspberrypi.com/documentation/computers/getting-started.html#installing-the-operating-system

#### Install OS into SD card
- install rpi-image on a host machine: `sudo snap install rpi-imager`
- Insert SD card within its adapter to the host machine's usb port
- start rpi-imager: `rpi-imager`
   - choose device: `Rasperry Pi 4 (Models B, 400, and Compute Modules 4, 4S)`
   - choose OS: `Raspberry Pi OS (64-bit)`
   - choose Storage: select the USB SD card
   - click `Next` button
   - Edit Settings:
       - Set hostname: `raspberry-pi`
       - Set username and password: Username: `montimage`
       - Services: check `Enable SSH`

#### Connect to 5G network
- Document: https://docs.sixfab.com/page/5g-lte-cellular-connectivity
- Turn on Quectel power
- SSH into the raspberry-pi
- Install software:
```bash
sudo apt purge modemmanager -y
sudo apt install -y minicom python3-pip

```
- Check USB driver of Quectel (note: need to *turn on* Quectel power switch) 
```bash
usb-devices
```

```bash
# Example:
montimage@raspberry-pi:~ $ usb-devices

...
T:  Bus=02 Lev=01 Prnt=01 Port=00 Cnt=01 Dev#=  2 Spd=5000 MxCh= 0
D:  Ver= 3.20 Cls=00(>ifc ) Sub=00 Prot=00 MxPS= 9 #Cfgs=  1
P:  Vendor=2c7c ProdID=0800 Rev=04.14
S:  Manufacturer=Quectel
S:  Product=RM502Q-AE
S:  SerialNumber=b773c683
C:  #Ifs= 5 Cfg#= 1 Atr=a0 MxPwr=896mA
....
```

- install atcom: https://github.com/sixfab/atcom
```bash
cd
# setup virtual environment for python3
python3 -m venv .venv
echo "export PATH=$HOME/.venv/bin":'$PATH' >> .bashrc
source .bashrc
# install atcom
pip install atcom
```


- setup Quectel: [ref](https://docs.sixfab.com/page/5g-lte-cellular-connectivity)
```bash
atcom --port /dev/ttyUSB2 'AT+QCFG="usbnet",1'
atcom --port /dev/ttyUSB2 'AT+CGDCONT=1,"IPV4V6","srsapn"'
atcom --port /dev/ttyUSB2 'AT+CFUN=1,1'
```

```bash
# Example:
# Enable USB NIC
montimage@raspberry-pi:~ $ atcom --port /dev/ttyUSB2 'AT+QCFG="usbnet",1'
AT+QCFG="usbnet",1
OK

# Set only your APN to connect the device to network
montimage@raspberry-pi:~ $ atcom --port /dev/ttyUSB2 'AT+CGDCONT=1,"IPV4V6","srsapn"'
AT+CGDCONT=1,"IPV4V6","srsapn"
OK

# Reset the modem
montimage@raspberry-pi:~ $ atcom --port /dev/ttyUSB2 'AT+CFUN=1,1'
AT+CFUN=1,1
OK
```

- Wait a few minutes for the modem to fully function and a few minutes for network registration, you should see a new NIC `usbX`:
```bash
montimage@raspberry-pi:~ $ ifconfig
eth0: flags=4163<UP,BROADCAST,RUNNING,MULTICAST>  mtu 1500
        inet 192.168.0.101  netmask 255.255.255.0  broadcast 192.168.0.255
        inet6 2a05:6e02:10a3:7010:4388:ea41:cd6e:a83c  prefixlen 64  scopeid 0x0<global>
        inet6 fe80::2365:978:4572:2035  prefixlen 64  scopeid 0x20<link>
        ether b8:27:eb:80:b2:fe  txqueuelen 1000  (Ethernet)
        RX packets 125  bytes 24854 (24.2 KiB)
        RX errors 0  dropped 0  overruns 0  frame 0
        TX packets 127  bytes 18886 (18.4 KiB)
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0

lo: flags=73<UP,LOOPBACK,RUNNING>  mtu 65536
        inet 127.0.0.1  netmask 255.0.0.0
        inet6 ::1  prefixlen 128  scopeid 0x10<host>
        loop  txqueuelen 1000  (Local Loopback)
        RX packets 18  bytes 2162 (2.1 KiB)
        RX errors 0  dropped 0  overruns 0  frame 0
        TX packets 18  bytes 2162 (2.1 KiB)
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0

usb0: flags=4163<UP,BROADCAST,RUNNING,MULTICAST>  mtu 1500
        inet 192.168.225.25  netmask 255.255.255.0  broadcast 192.168.225.255
        inet6 fe80::b6a0:d31b:9eb4:8e4a  prefixlen 64  scopeid 0x20<link>
        ether 1a:51:c4:b9:39:a8  txqueuelen 1000  (Ethernet)
        RX packets 17  bytes 1446 (1.4 KiB)
        RX errors 0  dropped 0  overruns 0  frame 0
        TX packets 36  bytes 4269 (4.1 KiB)
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0
```

- get UE connection status: 
```
atcom --port /dev/ttyUSB2 'AT+CGCONTRDP'
```

```bash
# Example
montimage@raspberry-pi:~ $ atcom --port /dev/ttyUSB2 'AT+CGCONTRDP'
AT+CGCONTRDP
+CGCONTRDP: 1,0,srsapn,10.45.0.11,,8.8.8.8,8.8.4.4

OK
```

- now you can use 5G connection
```bash
ping -I usb0 montimage.com -c 5
# or a simple speedtest
# curl --interface usb0 --output /dev/null https://www.releases.ubuntu.com/focal/ubuntu-20.04.6-live-server-amd64.iso 
```

```bash
# Example
montimage@raspberry-pi:~ $ ping -I usb0 montimage.com -c 5
PING montimage.com (217.70.184.55) from 192.168.225.25 usb0: 56(84) bytes of data.
64 bytes from webredir.gandi.net (217.70.184.55): icmp_seq=1 ttl=54 time=28.3 ms
64 bytes from webredir.gandi.net (217.70.184.55): icmp_seq=2 ttl=54 time=26.4 ms
64 bytes from webredir.gandi.net (217.70.184.55): icmp_seq=3 ttl=54 time=25.5 ms
64 bytes from webredir.gandi.net (217.70.184.55): icmp_seq=4 ttl=54 time=44.9 ms
64 bytes from webredir.gandi.net (217.70.184.55): icmp_seq=5 ttl=54 time=41.5 ms

--- montimage.com ping statistics ---
5 packets transmitted, 5 received, 0% packet loss, time 4007ms
rtt min/avg/max/mdev = 25.482/33.312/44.891/8.178 ms
```

- If UE has no Internet, then you need to add a [route](https://github.com/Montimage/docker-compose-srsran-open5gs-mmt/tree/main/docker/open5gs#usage) in the Core's host machine to the UEs' IP range
```bash
sudo iptables -t nat -A POSTROUTING -o enp0s31f6 -j MASQUERADE
sudo ip ro add 10.45.0.0/16 via 10.53.1.2
```

## Mobile UEs
- To obtain IP address and connect to Internet, COTS UEs need to use `srsapn` as acces point name: 
- add `Access Point Names`:
   - Name: `srsapn`
   - APN: `srsapn`




# TL;DR
(The commands below are to debug)

## test status of connection
montimage@raspberry-pi:~ $ atcom --port /dev/ttyUSB2 'AT+CGDCONT?'

RDY

+CPIN: READY

+QUSIM: 1

+CFUN: 1
AT+CGDCONT?
+CGDCONT: 1,"IPV4V6","internet","0.0.0.0.0.0.0.0.0.0.0.0.0.0.0.0",0,0,0,0,,,,,,,,,"",,,,0
+CGDCONT: 2,"IPV4V6","ims","0.0.0.0.0.0.0.0.0.0.0.0.0.0.0.0",0,0,0,0,,,,,,,,,"",,,,0
+CGDCONT: 3,"IPV4V6","sos","0.0.0.0.0.0.0.0.0.0.0.0.0.0.0.0",0,0,0,1,,,,,,,,,"",,,,0

OK

```

- setup Quectel: [ref](https://kb.ettus.com/OAI_Reference_Architecture_for_5G_and_6G_Research_with_USRP#AT_Commands_for_the_Module) 
```bash
# show firmware info
montimage@raspberry-pi:~ $ atcom --port /dev/ttyUSB2 'AT+GMR'
AT+GMR
RM502QAEAAR11A04M4G

OK

# show IMEI
montimage@raspberry-pi:~ $ atcom --port /dev/ttyUSB2 'AT+GSN'
AT+GSN
867826050908458

OK

# Unlock the Quectel module.
montimage@raspberry-pi:~ $ atcom --port /dev/ttyUSB2 'AT+QMBNCFG="select","ROW_Commercial"'
AT+QMBNCFG="select","ROW_Commercial"
OK

# Display which 5G NR frequency bands are configured.
montimage@raspberry-pi:~ $ atcom --port /dev/ttyUSB2 'AT+qnwprefcfg="nr5g_band"'
AT+qnwprefcfg="nr5g_band"
+QNWPREFCFG: "nr5g_band",1:2:3:5:7:8:12:20:25:28:38:40:41:48:66:71:77:78:79

OK

# Display which 5G NR mode is set.
montimage@raspberry-pi:~ $ atcom --port /dev/ttyUSB2 'AT+qnwprefcfg="mode_pref",nr5g'
AT+qnwprefcfg="mode_pref",nr5g
OK

# Set the operational mode to 5G NR SA.
montimage@raspberry-pi:~ $ atcom --port /dev/ttyUSB2 'AT+qnwprefcfg="nr5g_disable_mode",0'
AT+qnwprefcfg="nr5g_disable_mode",0
OK

# Enable 5G NR operations. 
montimage@raspberry-pi:~ $ atcom --port /dev/ttyUSB2 'AT+cgdcont=1,"IP","internet","0.0.0.0",0,0 (cid, pdp type, apn, pdp_addr, data_comp, head-comp)'
AT+cgdcont=1,"IP","internet","0.0.0.0",0,0 (cid, pdp type, apn, pdp_addr, data_comp, head-comp)
ERROR

# Set the minimum functionality of the module.
montimage@raspberry-pi:~ $ atcom --port /dev/ttyUSB2 'AT+CFUN=0'
AT+CFUN=0
OK

# Set the full functionality of the module.
montimage@raspberry-pi:~ $ atcom --port /dev/ttyUSB2 'AT+CFUN=1'
AT+CFUN=1
OK

```

- get status
```bash
# get IMSI
montimage@raspberry-pi:~ $ atcom --port /dev/ttyUSB2 AT+CIMI
AT+CIMI
001010000000004

OK

# check the APN is right and have an IP.
montimage@raspberry-pi:~ $ atcom --port /dev/ttyUSB2 'AT+CGCONTRDP'

+QUSIM: 1

+QIND: SMS DONE

+QIND: PB DONE
AT+CGCONTRDP
+CGCONTRDP: 1,0,internet,10.45.0.10,32.1.13.184.202.254.0.5.0.0.0.0.0.0.0.6, 254.128.0.0.0.0.0.0.0.0.0.0.0.0.0.1,8.8.8.8 32.1.72.96.72.96.0.0.0.0.0.0.0.0.136.136,8.8.4.4 32.1.72.96.72.96.0.0.0.0.0.0.0.0.136.68

OK
```

Got connection from Quectel to gNB, but the UE is released after few second because of `user-inactivity`

- Incite Quectel to reconnect:
```bash
montimage@raspberry-pi:~ $ atcom --port /dev/ttyUSB2 'AT+CFUN=0'
AT+CFUN=0
OK

montimage@raspberry-pi:~ $ atcom --port /dev/ttyUSB2 'AT+CFUN=1'
AT+CFUN=1
OK
```


#### Auo connect profile

- document: https://docs.sixfab.com/page/setting-up-a-data-connection-over-qmi-interface-using-libqmi
- commands from UM:
```bash
# Create a 3gpp profile and enable autoconnect
qmicli -d /dev/cdc-wdm0 --wds-set-autoconnect-settings=enabled,roaming-allowed
qmicli -d /dev/cdc-wdm0 --wds-create-profile=3gpp,apn=internet,pdp-type=IP,disabled=no,no-roaming=no
qmicli -d /dev/cdc-wdm0 --wds-set-default-profile-number=3gpp,<given profile #>
qmicli -d /dev/cdc-wdm0 --wds-get-profile-list=3gpp
qmicli -d /dev/cdc-wdm0 --wds-get-default-profile-number=3gpp
# Stablish data bearer
echo Y > /sys/class/net/wwan0/qmi/raw_ip
qmicli -p -d /dev/cdc-wdm0 --device-open-net='net-raw-ip|net-no-qos-header' --wds-start-network="apn='internet',ip-type=4" --client-no-release-cid
ip link set dev wwan0 up
udhcpc -q -f -i wwan0

# set network connection preference to a fixed SA PLMN
qmicli -d /dev/cdc-wdm0 --nas-set-system-selection-preference=5gnr,manual=00102
```

- tested on Raspberry Pi:
```bash
montimage@raspberry-pi:~ $ atcom --port /dev/ttyUSB2 'AT+QCFG="usbnet"'
AT+QCFG="usbnet"

+QCFG: "usbnet",0

OK

montimage@raspberry-pi:~ $ atcom --port /dev/ttyUSB2 'AT+QCFG="usbnet"'
AT+QCFG="usbnet"

+QCFG: "usbnet",0

OK

montimage@raspberry-pi:~ $ sudo qmicli -d /dev/cdc-wdm0 --dms-get-operating-mode
[/dev/cdc-wdm0] Operating mode retrieved:
	Mode: 'online'
	HW restricted: 'no'
montimage@raspberry-pi:~ $ qmicli -d /dev/cdc-wdm0 --wds-set-autoconnect-settings=enabled,roaming-allowed
error: couldn't open the QmiDevice: Cannot open device file '/dev/cdc-wdm0': Permission denied
montimage@raspberry-pi:~ $ sudo qmicli -d /dev/cdc-wdm0 --wds-set-autoconnect-settings=enabled,roaming-allowed
Autoconnect settings updated
montimage@raspberry-pi:~ $ sudo qmicli -d /dev/cdc-wdm0 --wds-create-profile=3gpp,apn=internet,pdp-type=IP,disabled=no,no-roaming=no
New profile created:
	Profile type: '3gpp'
	Profile index: '4'
montimage@raspberry-pi:~ $ sudo qmicli -d /dev/cdc-wdm0 --wds-set-default-profile-number=3gpp,4
Default profile number updated
montimage@raspberry-pi:~ $ qmicli -d /dev/cdc-wdm0 --wds-get-profile-list=3gpp
error: couldn't open the QmiDevice: Cannot open device file '/dev/cdc-wdm0': Permission denied
montimage@raspberry-pi:~ $ sudo qmicli -d /dev/cdc-wdm0 --wds-get-profile-list=3gpp
Profile list retrieved:
	[1] 3gpp - profile1
		APN: ''
		APN type: 'default'
		PDP type: 'ipv4-or-ipv6'
		PDP context number: '1'
		Username: ''
		Password: ''
		Auth: 'none'
		No roaming: 'no'
		APN disabled: 'no'
	[2] 3gpp - profile2
		APN: 'ims'
		APN type: 'ims'
		PDP type: 'ipv4-or-ipv6'
		PDP context number: '2'
		Username: ''
		Password: ''
		Auth: 'none'
		No roaming: 'no'
		APN disabled: 'no'
	[3] 3gpp - 
		APN: 'sos'
		APN type: 'emergency'
		PDP type: 'ipv4-or-ipv6'
		PDP context number: '3'
		Username: ''
		Password: ''
		Auth: 'none'
		No roaming: 'no'
		APN disabled: 'no'
	[4] 3gpp - 
		APN: 'internet'
		APN type: 'none'
		PDP type: 'ipv4'
		PDP context number: '4'
		Username: ''
		Password: ''
		Auth: 'none'
		No roaming: 'no'
		APN disabled: 'no'
montimage@raspberry-pi:~ $ sudo qmicli -d /dev/cdc-wdm0 --wds-get-default-profile-number=3gpp
Default profile number retrieved:
	Default profile number: '4'
montimage@raspberry-pi:~ $ echo 'Y' | sudo tee /sys/class/net/wwan0/qmi/raw_ip
Y
montimage@raspberry-pi:~ $ sudo qmicli -p -d /dev/cdc-wdm0 --device-open-net='net-raw-ip|net-no-qos-header' --wds-start-network="apn='internet',ip-type=4" --client-no-release-cid
error: couldn't start network: QMI protocol error (26): 'NoEffect'
[/dev/cdc-wdm0] Client ID not released:
	Service: 'wds'
	    CID: '16'
montimage@raspberry-pi:~ $ sudo ip link set dev wwan0 up
montimage@raspberry-pi:~ $ ip a
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
    inet 127.0.0.1/8 scope host lo
       valid_lft forever preferred_lft forever
    inet6 ::1/128 scope host noprefixroute 
       valid_lft forever preferred_lft forever
2: eth0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc mq state UP group default qlen 1000
    link/ether e4:5f:01:c0:f2:bc brd ff:ff:ff:ff:ff:ff
    inet 192.168.0.120/24 brd 192.168.0.255 scope global dynamic noprefixroute eth0
       valid_lft 42278sec preferred_lft 42278sec
    inet6 2a05:6e02:10a3:7010:3be8:268e:87c2:af42/64 scope global dynamic noprefixroute 
       valid_lft 86345sec preferred_lft 86345sec
    inet6 fe80::8fbf:40c1:7009:7c17/64 scope link noprefixroute 
       valid_lft forever preferred_lft forever
4: wlan0: <NO-CARRIER,BROADCAST,MULTICAST,UP> mtu 1500 qdisc pfifo_fast state DOWN group default qlen 1000
    link/ether e4:5f:01:c0:f2:bd brd ff:ff:ff:ff:ff:ff
6: wwan0: <POINTOPOINT,MULTICAST,NOARP,UP,LOWER_UP> mtu 1500 qdisc pfifo_fast state UNKNOWN group default qlen 1000
    link/none 
    inet6 fe80::fd73:9173:f998:24c2/64 scope link stable-privacy 
       valid_lft forever preferred_lft forever
montimage@raspberry-pi:~ $ sudo qmicli -d /dev/cdc-wdm0 --nas-set-system-selection-preference=5gnr,manual=00101
[/dev/cdc-wdm0] System selection preference set successfully; replug your device.

# turn off, then on the Quectel card

```

#### Other commands:

```bash
# list all available operators:

montimage@raspberry-pi:~ $ atcom --timeout 120 --port /dev/ttyUSB2 'AT+COPS=?'

AT+COPS=?
+COPS: (1,"MMT","MMT","00101",12),(1,"F SFR","SFR","20810",7),(1,"F SFR","SFR","20810",2),(3,"F-Bouygues Telecom","BYTEL","20820",2),(3,"Orange F","Orange","20801",7),(3,"Free","Free","20815",7),(3,"F-Bouygues Telecom","BYTEL","20820",7),(3,"Orange F","Orange","20801",2),(3,"Free","Free","20816",7),(3,"Free","Free","20815",2),,(0-4),(0-2)

OK

```
