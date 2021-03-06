Using the LORANGA board
=======================

The [LORANGA](https://www.alegrefactory.com/loranga) board from "La Fabrica Alegre" company embeds an RFM95W as the LoRa module. This module needs PABOOST so in order to use the module you first have to edit radio.makefile and make sure that PABOOST is enabled:

	CFLAGS=-DPABOOST -DMAX_DBM=14
	
Then, run the scripts/basic_config_gw.sh to both configure your gateway and compile the loga_gateway program.	

Starting Internet connection
----------------------------

The support of [LORANGA](https://www.alegrefactory.com/loranga) board has been kindly provided by the team from "La Fabrica Alegre" company.

They have the following [test github](https://github.com/edu986/test_ppp) to explain the installation of the support of the LORANGA board. Note that the wvdial method explained for 3G USB dongles is currently not working with the LORANGA board.

If you use our latest version of SD card image (from September 2017) then everything is set-up to use the LORANGA board. You just have to run:

	> cd 3GDongle/loranga
	> ./start-internet.sh

You can add the following lines in your /etc/rc.local file to have it started at boot:

	cd /home/pi/lora_gateway/3GDongle/loranga
	start-internet.sh
	
Normally after the following line:

	/home/pi/lora_gateway/scripts/start_gw.sh

However, it is safer to test it first in interactive mode. Use stop-internet.sh to stop the pppd process and power-off the LORANGA board. Once you are supposed to have Internet, try to ping some remote computers.

Sending SMS
-----------

In order to use the LORANGA board for sending SMS with CloudSMS.py, you just have to edit (or create) in your /home/pi folder the .gammurc file:

	[gammu]
	device = /dev/ttyS0
	name = loranga
	connection = at
	
That should work, even if gammu-detect is not returning anything. If you want to try if SMS support is working, first kill all gateway processes (option K of cmd.sh) then edit clouds.json to enable CloudSMS.py:

	{	
		"name":"SMS Service",
		"script":"python CloudSMS.py",
		"type":"smsservice",
		"gammurc_file":"/home/pi/.gammurc",
		"enabled":true,
		"always_enabled":true
	},

Then edit key_SMS.py to indicate the phone number that will receive the SMS (it is better to disable the SIM card PIN with your mobile phone prior to insert the SIM card in the LORANGA board):

	contacts=["+33xxxxxxxxx"]
		
Then start manually the gateway:

	> sudo python start_gw.py
	
And use an end-device (our interactive device for instance) to send "\!TC/22.5" to the gateway. You should see something like:

	--- rxlora. dst=1 type=0x10 src=6 seq=2 len=9 SNR=6 RSSIpkt=-57 BW=125 CR=4/5 SF=12
	2017-09-02T09:47:19.960920> 2017-09-02T09:47:19.960399
	2017-09-02T09:47:19.961140> rcv ctrl pkt info (^p): 1,16,6,2,9,6,-57
	2017-09-02T09:47:19.961329> splitted in:  [1, 16, 6, 2, 9, 6, -57]
	2017-09-02T09:47:19.961516> (dst=1 type=0x10(DATA) src=6 seq=2 len=9 SNR=6 RSSI=-57)
	2017-09-02T09:47:19.961701> rcv ctrl radio info (^r): 125,5,12
	2017-09-02T09:47:19.961889> splitted in:  [125, 5, 12]
	2017-09-02T09:47:19.962099> (BW=125 CR=5 SF=12)
	2017-09-02T09:47:19.962445> rcv timestamp (^t): 2017-09-02T09:47:19.959
	2017-09-02T09:47:19.962670> 
	2017-09-02T09:47:19.962868> got first framing byte
	2017-09-02T09:47:19.963050> --> got data prefix
	2017-09-02T09:47:19.963252> valid app key: accept data
	2017-09-02T09:47:19.963462> number of enabled clouds is 1
	2017-09-02T09:47:19.963661> --> cloud[0]
	2017-09-02T09:47:19.963860> uploading with python CloudSMS.py
	2017-09-02T09:47:38.567588> rcv msg to send via the SMS Service: SensorData Sensor6 SNR 6 RSSI -57 SN 2 BW 125 CR 5 SF 12 GWID 00000027EBBEDA21 TC 22.5
	2017-09-02T09:47:38.567983> Sent to +33xxxxxxxxx successfully!
	2017-09-02T09:47:38.568188> Sending SMS done
	2017-09-02T09:47:38.579110> python CloudSMS.py "TC/22.5" "1,16,6,2,9,6,-57" "125,5,12" "2017-09-02T09:47:19.959" "00000027EBBEDA21"
	2017-09-02T09:47:38.579381> --> cloud end

However, currently, it is not possible to have Internet connectivity and SMS support at the same time.

Raspberry PI Zero
-----------------
	
If you use the latest image, it also works on the new Raspberry Zero W (with the default behavior to act as a WiFi access point so you can easily use ssh pi@192.168.200.1 once you are connected to the gateway's WiFi). It can therefore be used to build and even lower-cost and more compact LoRa gateway. Note that regarding the serial port, the Raspberry PI Zero W is similar to the Raspberry PI 3, so /dev/ttyS0 is used, and not /dev/ttyAMA0.		

Enjoy,
C. Pham
		