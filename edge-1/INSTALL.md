# Hardware: Superserver-E403-12P-FN2T
- 32 Cores
- 64 GB of RAM 

# Environment: Ubuntu server 20.04

```bash
$ lsb_release -a
No LSB modules are available.
Distributor ID:	Ubuntu
Description:	Ubuntu 20.04.6 LTS
Release:	20.04
Codename:	focal
```

# Install srsRAN + open5Gs
- 08 January 2024 

## UHD driver:

- documentation: https://files.ettus.com/manual/page_install.html

- installation:
```bash
montimage@montimage-edge-1:~$ sudo add-apt-repository ppa:ettusresearch/uhd
montimage@montimage-edge-1:~$ sudo apt-get install libuhd-dev uhd-host

# Test
montimage@montimage-edge-1:~$ sudo uhd_images_downloader 
[INFO] Using base URL: https://files.ettus.com/binaries/cache/
[INFO] Images destination: /usr/share/uhd/images
[INFO] No inventory file found at /usr/share/uhd/images/inventory.json. Creating an empty one.
32419 kB / 32419 kB (100%) x4xx_x410_fpga_default-ge547a6b.zip
53458 kB / 53458 kB (100%) x4xx_x440_fpga_default-ge547a6b.zip
21602 kB / 21602 kB (100%) x3xx_x310_fpga_default-g6a990d9.zip
19738 kB / 19738 kB (100%) x3xx_x300_fpga_default-g6a990d9.zip
01126 kB / 01126 kB (100%) e3xx_e310_sg1_fpga_default-g6a990d9.zip
01118 kB / 01118 kB (100%) e3xx_e310_sg3_fpga_default-g6a990d9.zip
10196 kB / 10196 kB (100%) e3xx_e320_fpga_default-g6a990d9.zip
20842 kB / 20842 kB (100%) n3xx_n310_fpga_default-gd6608fc.zip
14257 kB / 14257 kB (100%) n3xx_n300_fpga_default-gd6608fc.zip
27244 kB / 27244 kB (100%) n3xx_n320_fpga_default-gd6608fc.zip
00481 kB / 00481 kB (100%) b2xx_b200_fpga_default-g92c09f7.zip
00464 kB / 00464 kB (100%) b2xx_b200mini_fpga_default-g92c09f7.zip
00883 kB / 00883 kB (100%) b2xx_b210_fpga_default-g92c09f7.zip
00511 kB / 00511 kB (100%) b2xx_b205mini_fpga_default-g92c09f7.zip
00167 kB / 00167 kB (100%) b2xx_common_fw_default-g7f7d016.zip
00007 kB / 00007 kB (100%) usrp2_usrp2_fw_default-g6bea23d.zip
00450 kB / 00450 kB (100%) usrp2_usrp2_fpga_default-g6bea23d.zip
02415 kB / 02415 kB (100%) usrp2_n200_fpga_default-g6bea23d.zip
00009 kB / 00009 kB (100%) usrp2_n200_fw_default-g6bea23d.zip
02757 kB / 02757 kB (100%) usrp2_n210_fpga_default-g6bea23d.zip
00009 kB / 00009 kB (100%) usrp2_n210_fw_default-g6bea23d.zip
00319 kB / 00319 kB (100%) usrp1_usrp1_fpga_default-g6bea23d.zip
00522 kB / 00522 kB (100%) usrp1_b100_fpga_default-g6bea23d.zip
00006 kB / 00006 kB (100%) usrp1_b100_fw_default-g6bea23d.zip
00017 kB / 00017 kB (100%) octoclock_octoclock_fw_default-g14000041.zip
04839 kB / 04839 kB (100%) usb_common_windrv_default-g14000041.zip
[INFO] Images download complete.
montimage@montimage-edge-1:~$ uhd_usrp_probe 
[INFO] [UHD] linux; GNU C++ version 9.4.0; Boost_107100; UHD_4.6.0.0-0ubuntu1~focal1
[ERROR] [USB] USB open failed: insufficient permissions.
See the application notes for your device.
Error: LookupError: KeyError: No devices found for ----->
Empty Device Address
montimage@montimage-edge-1:~$ sudo uhd_usrp_probe 
[INFO] [UHD] linux; GNU C++ version 9.4.0; Boost_107100; UHD_4.6.0.0-0ubuntu1~focal1
[INFO] [B200] Loading firmware image: /usr/share/uhd/images/usrp_b200_fw.hex...
[INFO] [B200] Detected Device: B210
[INFO] [B200] Loading FPGA image: /usr/share/uhd/images/usrp_b210_fpga.bin...
[INFO] [B200] Operating over USB 3.
[INFO] [B200] Detecting internal GPSDO.... 
[INFO] [GPS] No GPSDO found
[INFO] [B200] Initialize CODEC control...
[INFO] [B200] Initialize Radio control...
[INFO] [B200] Performing register loopback test... 
[INFO] [B200] Register loopback test passed
[INFO] [B200] Performing register loopback test... 
[INFO] [B200] Register loopback test passed
[INFO] [B200] Setting master clock rate selection to 'automatic'.
[INFO] [B200] Asking for clock rate 16.000000 MHz... 
[INFO] [B200] Actually got clock rate 16.000000 MHz.
  _____________________________________________________
 /
|       Device: B-Series Device
|     _____________________________________________________
|    /
|   |       Mboard: B210
|   |   serial: 3153210
|   |   name: MyB210
|   |   product: 2
|   |   revision: 4
|   |   FW Version: 8.0
|   |   FPGA Version: 16.0
|   |   
|   |   Time sources:  none, internal, external, gpsdo
|   |   Clock sources: internal, external, gpsdo
|   |   Sensors: ref_locked
|   |     _____________________________________________________
|   |    /
|   |   |       RX DSP: 0
|   |   |   
|   |   |   Freq range: -8.000 to 8.000 MHz
|   |     _____________________________________________________
|   |    /
|   |   |       RX DSP: 1
|   |   |   
|   |   |   Freq range: -8.000 to 8.000 MHz
|   |     _____________________________________________________
|   |    /
|   |   |       RX Dboard: A
|   |   |     _____________________________________________________
|   |   |    /
|   |   |   |       RX Frontend: A
|   |   |   |   Name: FE-RX2
|   |   |   |   Antennas: TX/RX, RX2
|   |   |   |   Sensors: temp, rssi, lo_locked
|   |   |   |   Freq range: 50.000 to 6000.000 MHz
|   |   |   |   Gain range PGA: 0.0 to 76.0 step 1.0 dB
|   |   |   |   Bandwidth range: 200000.0 to 56000000.0 step 0.0 Hz
|   |   |   |   Connection Type: IQ
|   |   |   |   Uses LO offset: No
|   |   |     _____________________________________________________
|   |   |    /
|   |   |   |       RX Frontend: B
|   |   |   |   Name: FE-RX1
|   |   |   |   Antennas: TX/RX, RX2
|   |   |   |   Sensors: temp, rssi, lo_locked
|   |   |   |   Freq range: 50.000 to 6000.000 MHz
|   |   |   |   Gain range PGA: 0.0 to 76.0 step 1.0 dB
|   |   |   |   Bandwidth range: 200000.0 to 56000000.0 step 0.0 Hz
|   |   |   |   Connection Type: IQ
|   |   |   |   Uses LO offset: No
|   |   |     _____________________________________________________
|   |   |    /
|   |   |   |       RX Codec: A
|   |   |   |   Name: B210 RX dual ADC
|   |   |   |   Gain Elements: None
|   |     _____________________________________________________
|   |    /
|   |   |       TX DSP: 0
|   |   |   
|   |   |   Freq range: -8.000 to 8.000 MHz
|   |     _____________________________________________________
|   |    /
|   |   |       TX DSP: 1
|   |   |   
|   |   |   Freq range: -8.000 to 8.000 MHz
|   |     _____________________________________________________
|   |    /
|   |   |       TX Dboard: A
|   |   |     _____________________________________________________
|   |   |    /
|   |   |   |       TX Frontend: A
|   |   |   |   Name: FE-TX2
|   |   |   |   Antennas: TX/RX
|   |   |   |   Sensors: temp, lo_locked
|   |   |   |   Freq range: 50.000 to 6000.000 MHz
|   |   |   |   Gain range PGA: 0.0 to 89.8 step 0.2 dB
|   |   |   |   Bandwidth range: 200000.0 to 56000000.0 step 0.0 Hz
|   |   |   |   Connection Type: IQ
|   |   |   |   Uses LO offset: No
|   |   |     _____________________________________________________
|   |   |    /
|   |   |   |       TX Frontend: B
|   |   |   |   Name: FE-TX1
|   |   |   |   Antennas: TX/RX
|   |   |   |   Sensors: temp, lo_locked
|   |   |   |   Freq range: 50.000 to 6000.000 MHz
|   |   |   |   Gain range PGA: 0.0 to 89.8 step 0.2 dB
|   |   |   |   Bandwidth range: 200000.0 to 56000000.0 step 0.0 Hz
|   |   |   |   Connection Type: IQ
|   |   |   |   Uses LO offset: No
|   |   |     _____________________________________________________
|   |   |    /
|   |   |   |       TX Codec: A
|   |   |   |   Name: B210 TX dual DAC
|   |   |   |   Gain Elements: None
```

## srsRAN
- documentation: https://docs.srsran.com/projects/project/en/latest/user_manuals/source/installation.html#ubuntu
- installation:

```bash
montimage@montimage-edge-1:~$ sudo add-apt-repository ppa:softwareradiosystems/srsran-project
 srsRAN Project PPA
 More info: https://launchpad.net/~softwareradiosystems/+archive/ubuntu/srsran-project
Press [ENTER] to continue or Ctrl-c to cancel adding it.
Hit:1 http://fr.archive.ubuntu.com/ubuntu focal InRelease
Hit:2 http://fr.archive.ubuntu.com/ubuntu focal-updates InRelease
Hit:3 http://fr.archive.ubuntu.com/ubuntu focal-backports InRelease
Hit:4 http://fr.archive.ubuntu.com/ubuntu focal-security InRelease
Hit:5 http://ppa.launchpad.net/ettusresearch/uhd/ubuntu focal InRelease
Get:6 http://ppa.launchpad.net/softwareradiosystems/srsran-project/ubuntu focal InRelease [18.1 kB]
Get:7 http://ppa.launchpad.net/softwareradiosystems/srsran-project/ubuntu focal/main amd64 Packages [580 B]
Get:8 http://ppa.launchpad.net/softwareradiosystems/srsran-project/ubuntu focal/main Translation-en [332 B]
Fetched 19.0 kB in 1s (14.1 kB/s)       
Reading package lists... Done
montimage@montimage-edge-1:~$ sudo apt-get install srsran-project -y
Reading package lists... Done
Building dependency tree       
Reading state information... Done
The following NEW packages will be installed:
  srsran-project
0 upgraded, 1 newly installed, 0 to remove and 56 not upgraded.
Need to get 11.8 MB of archives.
After this operation, 59.8 MB of additional disk space will be used.
Get:1 http://ppa.launchpad.net/softwareradiosystems/srsran-project/ubuntu focal/main amd64 srsran-project amd64 23.5-0ubuntu1ppa1~20.04 [11.8 MB]
Fetched 11.8 MB in 0s (39.4 MB/s)       
Selecting previously unselected package srsran-project.
(Reading database ... 132689 files and directories currently installed.)
Preparing to unpack .../srsran-project_23.5-0ubuntu1ppa1~20.04_amd64.deb ...
Unpacking srsran-project (23.5-0ubuntu1ppa1~20.04) ...
Setting up srsran-project (23.5-0ubuntu1ppa1~20.04) ...
```

### open5Gs
- documentation: https://open5gs.org/open5gs/docs/guide/01-quickstart/


### MongoDB 6.0
```bash
montimage@montimage-edge-1:~$ echo "deb [ arch=amd64,arm64 signed-by=/usr/share/keyrings/mongodb-server-6.0.gpg ] https://repo.mongodb.org/apt/ubuntu focal/mongodb-org/6.0 multiverse" | sudo tee /etc/apt/sources.list.d/mongodb-org-6.0.list
deb [ arch=amd64,arm64 signed-by=/usr/share/keyrings/mongodb-server-6.0.gpg ] https://repo.mongodb.org/apt/ubuntu focal/mongodb-org/6.0 multiverse
montimage@montimage-edge-1:~$ sudo apt update
Hit:1 http://ppa.launchpad.net/ettusresearch/uhd/ubuntu focal InRelease
Hit:2 http://fr.archive.ubuntu.com/ubuntu focal InRelease
Hit:3 http://ppa.launchpad.net/softwareradiosystems/srsran-project/ubuntu focal InRelease
Hit:4 http://fr.archive.ubuntu.com/ubuntu focal-updates InRelease
Hit:5 http://fr.archive.ubuntu.com/ubuntu focal-backports InRelease
Ign:6 https://repo.mongodb.org/apt/ubuntu focal/mongodb-org/6.0 InRelease
Hit:7 http://fr.archive.ubuntu.com/ubuntu focal-security InRelease       
Get:8 https://repo.mongodb.org/apt/ubuntu focal/mongodb-org/6.0 Release [3094 B]
Get:9 https://repo.mongodb.org/apt/ubuntu focal/mongodb-org/6.0 Release.gpg [866 B]
Get:10 https://repo.mongodb.org/apt/ubuntu focal/mongodb-org/6.0/multiverse arm64 Packages [44.0 kB]
Get:11 https://repo.mongodb.org/apt/ubuntu focal/mongodb-org/6.0/multiverse amd64 Packages [47.3 kB]
Fetched 95.2 kB in 2s (57.8 kB/s)    
Reading package lists... Done
Building dependency tree       
Reading state information... Done
56 packages can be upgraded. Run 'apt list --upgradable' to see them.
montimage@montimage-edge-1:~$ sudo apt-get install -y mongodb-org
…
montimage@montimage-edge-1:~$ sudo systemctl start mongod 
```

### Open5Gs

```bash
montimage@montimage-edge-1:~$ sudo add-apt-repository ppa:open5gs/latest
 Open5GS is a C-language Open Source implementation of 5G Core and EPC, i.e. the core network of NR/LTE network (Release-17)
 More info: https://launchpad.net/~open5gs/+archive/ubuntu/latest
Press [ENTER] to continue or Ctrl-c to cancel adding it.
Ign:1 https://repo.mongodb.org/apt/ubuntu focal/mongodb-org/6.0 InRelease
Hit:2 http://ppa.launchpad.net/ettusresearch/uhd/ubuntu focal InRelease
Hit:3 http://fr.archive.ubuntu.com/ubuntu focal InRelease
Get:4 http://ppa.launchpad.net/open5gs/latest/ubuntu focal InRelease [18.1 kB]
Hit:5 http://fr.archive.ubuntu.com/ubuntu focal-updates InRelease
Hit:6 http://ppa.launchpad.net/softwareradiosystems/srsran-project/ubuntu focal InRelease
Hit:7 http://fr.archive.ubuntu.com/ubuntu focal-backports InRelease
Hit:8 http://fr.archive.ubuntu.com/ubuntu focal-security InRelease
Hit:9 https://repo.mongodb.org/apt/ubuntu focal/mongodb-org/6.0 Release
Get:10 http://ppa.launchpad.net/open5gs/latest/ubuntu focal/main amd64 Packages [3336 B]
Get:11 http://ppa.launchpad.net/open5gs/latest/ubuntu focal/main Translation-en [1700 B]
Fetched 23.1 kB in 1s (16.5 kB/s)        
Reading package lists... Done
montimage@montimage-edge-1:~$ sudo apt install open5gs
Reading package lists... Done
Building dependency tree       
Reading state information... Done
The following additional packages will be installed:
  libbson-1.0-0 libidn11 libmicrohttpd12 libmongoc-1.0-0 libsnappy1v5 libtalloc2 libtins4.0 open5gs-amf open5gs-ausf
  open5gs-bsf open5gs-common open5gs-hss open5gs-mme open5gs-nrf open5gs-nssf open5gs-pcf open5gs-pcrf open5gs-scp
  open5gs-sgwc open5gs-sgwu open5gs-smf open5gs-udm open5gs-udr open5gs-upf
The following NEW packages will be installed:
  libbson-1.0-0 libidn11 libmicrohttpd12 libmongoc-1.0-0 libsnappy1v5 libtalloc2 libtins4.0 open5gs open5gs-amf open5gs-ausf
  open5gs-bsf open5gs-common open5gs-hss open5gs-mme open5gs-nrf open5gs-nssf open5gs-pcf open5gs-pcrf open5gs-scp
  open5gs-sgwc open5gs-sgwu open5gs-smf open5gs-udm open5gs-udr open5gs-upf
0 upgraded, 25 newly installed, 0 to remove and 56 not upgraded.
Need to get 4561 kB of archives.
After this operation, 23.3 MB of additional disk space
```

### GUI of open5GS to manage subscribers via Web interface
```bash
sudo apt update
sudo apt install -y ca-certificates curl gnupg
sudo mkdir -p /etc/apt/keyrings
curl -fsSL https://deb.nodesource.com/gpgkey/nodesource-repo.gpg.key | sudo gpg --dearmor -o /etc/apt/keyrings/nodesource.gpg

NODE_MAJOR=20
echo "deb [signed-by=/etc/apt/keyrings/nodesource.gpg] https://deb.nodesource.com/node_$NODE_MAJOR.x nodistro main" | sudo tee /etc/apt/sources.list.d/nodesource.list

sudo apt update
sudo apt install nodejs -y

curl -fsSL https://open5gs.org/open5gs/assets/webui/install | sudo -E bash -
```
