# TVheadend

--Build--

git clone https://github.com/tvheadend/tvheadend

apt install dependencies

AUTOBUILD_CONFIGURE_EXTRA="--enable-or-disable-bundle" ./Autobuild.sh 

Note: the generated deb will be in the directory above that containing Autobuild.sh (i.e. outside of the tvheadend directory).

Delta hoofd frequentie
```
[Main Frequency]
	DELIVERY_SYSTEM = DVBC/ANNEX_A
	FREQUENCY = 538000000
	SYMBOL_RATE = 6875000
	INNER_FEC = NONE
	MODULATION = QAM/64
```
	
 Voor PIcons > set 'digest+plain' HTTP authentication
  
--CA setup--

Type: DVBapi
Mode: Oscam net protocol 
IP: fill in hostname or ip
Port: 9000
Listen: Standard / auto

# OScam
```
oscam.conf

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
```
oscam.server

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
```
oscam.user

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
