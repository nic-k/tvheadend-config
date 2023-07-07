# TVheadend

TVheadend clonen en compileren

Info: https://tvheadend.org/boards/4/topics/24116

```
git clone https://github.com/tvheadend/tvheadend

sudo apt install build-essential git ccache libpcre2-dev pkg-config libssl-dev bzip2 \
wget libavahi-client-dev zlib1g-dev libavcodec-dev libavutil-dev libavformat-dev \
libswscale-dev libavresample-dev gettext cmake libiconv-hook-dev liburiparser-dev \
debhelper libcurl4-gnutls-dev python-minimal libdvbcsa-dev python-requests \
dvb-apps libva-dev libva-drm1 libva-x11-1 \

sudo apt install python-is-python3

./Autobuild.sh 
```

Om vaapi transcoding te installer is libva-dev nodig
```
sudo apt install libva-dev
```

Om opties in of uit schakelen kan het volgende commando worden gebruikt
```
AUTOBUILD_CONFIGURE_EXTRA="--disable-libvpx --disable-libvpx_static" ./Autobuild.sh 
```

Note: Het deb package staat een folder hoger dan autobuild

Delta hoofd frequentie
```
[Main Frequency]
	DELIVERY_SYSTEM = DVBC/ANNEX_A
	FREQUENCY = 538000000
	SYMBOL_RATE = 6875000
	INNER_FEC = NONE
	MODULATION = QAM/64
```
  
CA setup
```
Type: DVBapi
Mode: Oscam net protocol 
IP: fill in hostname or ip
Port: 9000
Listen: Standard / auto
```

# OScam

Installeer oscam volgens instructies https://sabrnet.wzk.cz/2019/02/oscam-installation-on-raspberry-pi-raspbian-stretch-debian-or-ubuntu/

oscam.conf
```
[global]
logfile                       = stdout

[newcamd]
port                          = 15050@0604:048108
key                           = 0102030405060708091011121314
keepalive                     = 1

[dvbapi]
enabled                       = 1
au                            = 1
pmt_mode                      = 3
listen_port                   = 9000
user                          = tvheadend1
boxtype                       = pc

[webif]
httpport                      = 8888
httpallowed                   = 127.0.0.1,192.168.0.0-192.168.255.255,10.0.0.0-10.255.255.255,255.255.255.255
```

oscam.server
```
[reader]
label                         = Reader-0
description                   = Delta DVB-C
protocol                      = smartreader
device                        = 002:004
caid                          = 0604
boxkey                        = 1122334455667788
rsakey                        = 3C8633AAC0D367533DEC7BB2EEEDEB8CA3ADA52E58B99BB34672783277A1DAAC3B6106AD0909774E031B2A6E30195B437683AD0FC599B87D08CEA47BE1B6C76A
detect                        = cd
mhz                           = 600
cardmhz                       = 600
group                         = 1
emmcache                      = 1,3,2,0
```

oscam.user
```
[account]
user                          = tvheadend
pwd                           = tvheadend
description                   = local tvheadend server
caid                          = 0604
au                            = Reader-0
group                         = 1
max_connections               = 5

[account]
user                          = tvheadend1
pwd                           = tvheadend1
au                            = Reader-0
group                         = 1
```

# Picons

In TVheadend moet de inlog methode worden veranderd voor Picons 
```
configuration > general > base > HTTP authentication: 'digest+plain' 
```

Stel daarna het picons path in en geef de juiste rechten

```
sudo mkdir /home/hts/picons
sudo chown hts:hts /home/hts/picons/

configuration > general > base > Picon path: 'file:///home/hts/picons'  
```

Download Picons 
```
# Ubuntu, Bash on Ubuntu on Windows
sudo apt-get install git binutils pngquant imagemagick librsvg2-bin jq

# Ubuntu
git clone https://github.com/picons/picons.git ~/picons
```

Stel build-input in

Maak nieuw bestand in build-input folder met naam tvheadend.serverconf.

```
# hostname or ip address of tvheadend server (default: "localhost")
TVH_HOST="localhost"
# port of tvheadend API (default: 9981)
TVH_PORT="9981"
# tvheadend user name
TVH_USER="jamie"
# tvheadend password of above user
TVH_PASS=""
```

Stel build-output in

Maak nieuw bestand in build-output folder met naam backgrounds.conf.

```
#################################################################
### <resolution>;<resolution-padding>;<logotype>;<background> ###
#################################################################

256x256;256x256;light;transparent
```

Start 1-build-servicelist.sh. Kies 1 en enter.

```
$ bash 1-build-servicelist.sh

Which style are you going to build?
1) Service Reference
2) Service Name
#?
```

Start 2-build-picons.sh. Kies 1 en enter.Aqqqq

```
$ bash 2-build-picons.sh

Log file located at: /tmp/tmp.cVWBKTq9Yh.picons.log

16:06:23 - No "svgconverter.conf" file found in "build-input", using default file!
Which style are you going to build?
1) Service Reference         3) Service Name
2) Service Reference (Full)  4) Service Name (Full)
#?
```

De icons staan in een binaries-srp.tar in de build-output folder. Pak de hardlink versie uit en kopieer deze naar de tvheadend picons folder

```
cd build-output/binaries-srp/
tar xvf srp.256x256-256x256.light.on.transparent_2022-01-19--18-40-56.hardlink.tar.xz
cd srp.256x256-256x256.light.on.transparent_2022-01-19--18-40-56/
sudo cp * /home/hts/picons
sudo chown hts:hts /home/hts/picons/*
