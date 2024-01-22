

- MCC: 999
- MNC: 14
- MNC_LENGTH: 2
- TAC: 1
- KEY: 1102030405060708090a0b0c0d0e0f00
- OP: bc2bce2a23be2fae32e4f1b4546004f7
- USIM Type: OP
- UE1: 999140000000001


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
- Check USB driver of Quectel: `usb-devices`
```bash
$ usb-devices

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
atcom --port /dev/ttyUSB2 'AT+CGDCONT=1,"IPV4V6","internet"'
atcom --port /dev/ttyUSB2 'AT+CFUN=1,1'
```

Example:
```bash
montimage@raspberry-pi:~ $ atcom --port /dev/ttyUSB2 'AT+QCFG="usbnet",1'
AT+QCFG="usbnet",1
OK

montimage@raspberry-pi:~ $ atcom --port /dev/ttyUSB2 'AT+CGDCONT=1,"IPV4V6","internet"'
AT+CGDCONT=1,"IPV4V6","internet"
OK

montimage@raspberry-pi:~ $ atcom --port /dev/ttyUSB2 'AT+CFUN=1,1'
AT+CFUN=1,1
OK

# test status of connection
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