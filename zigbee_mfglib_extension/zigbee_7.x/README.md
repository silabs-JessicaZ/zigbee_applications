# Manufacturing Library Extension #

## 1. Summary ##

While there is a currently function manufacturing library plugin, the following code extends this library with more functionality and commands. Sending and receiving packets, PER test, manipulating tokens, configuring GPIO settings, and changing the power to deci dBm are among the added functionality of this code. This project was built and tested on the Wireless Starter Kit Main Board (BRD4001) and EFR32MG21 2.4 GHz 10 dBm Radio Board (BRD4181A) in Studio v5 with UC. This tutorial assumes these are the boards being used. This code can be built on any EFR32 board but may require adjustments. 

| Command  	| Command Description 	| API Function  | Argument: 	|  	|  		|  
| ------------- | ------------- 	| ------ 	|  ----- 	| --- 	| --- 		|
|	|	     	|	|**Name** |**Type**	|**Description**|
|clear-rx-packets|Reset the number of received packets to 0.|emAfMfglibClearPackets	|N/A	|N/A|	|
|set-deci-dbm	|Sets the power of radio transmissions(deci-dBm).	|emAfMfglibSetPower	|powerLevel|INT32S|The power in deci-dBm|
|get-deci-dbm	|Gets the power of radio transmissions(deci-dBm).	|emAfMfglibGetPower	|N/A	|N/A	|	 |
|set-cca	|Sets the temporary register value of the CCA threshold.	|emAfMfglibSetCcaThresholdReg	|threshold	|INT8S	|The temporary register value|
|get-cca	|Gets the temporary register value of the CCA threshold.	|emAfMfglibGetCcaThresholdReg	|N/A |N/A	|	|
|get-ctune-reg	|Gets the temporary register of the CTUNE value.	|emAfMfglibGetCtuneValueReg	|N/A	|N/A	| 	|
|set-ctune-reg	|Sets the temporary register of the CTUNE value. |Use the Ctune register to control the crystal's capacitance load which can adjust the frequency.	|emAfMfglibSetCtuneValueReg	|tune	|INT16U	|The temporary register value|
|get-cca-tok	|Gets the token value of the CCA threshold.	|emAfMfglibGetCcaThresholdTok	|N/A	|N/A	| 	|
|set-cca-tok	|Sets the token value of the CCA threshold. The default CCA threshold of 2.4GHz is -75dBm. The CCA (clear channel assessment)  listens for RF transmissions at the physical layer to see if the medium is idle or not.	|emAfMfglibSetCcaThresholdTok	|threshold_new	|INT16U	|CCA Threshold token value|
|get-ctune-tok|Gets the token of the CTUNE value.	|emAfMfglibGetCtuneValueTok	|N/A	|N/A	|	|
|set-ctune-tok	|Sets the token of the CTUNE value. |This token is for tuning the EFR32 system XTAL and consequently also tunes the radio synthesizer frequency.	|emAfMfglibSetCtuneValueTok	|value_new	|INT16U	|CTUNE token value|
|set-gpio |Sets various GPIO(general-purpose input/output) settings.	|emAfMfglibSetGpio	|port	|GPIO_Port_TypeDef| Available ports: 0-10 |
|	|								|			|pin	|INT32U			|Available pins: 0-15|
|	|								|			|mode	|GPIO_Mode_TypeDef	|Available modes: 0-15|
|	|								|			|out	|INT32U			|Available outs: 0-1|
|get-gpio	|Gets GPIO mode at a specified GPIO port and pin.	|emAfMfglibGetGpio	|port	|GPIO_Mode_TypeDef	|Available ports: 0-10|
|	|								|			|pin	|INT32U			|Available pins: 0-15|
|gpio-help	|Gives information of GPIO command inputs.		|emAfMfglibGpioHelp	|N/A	|N/A			| |
|tok-dump	|Dumps all tokens.					|emAfMfglibTokDump	|N/A	|N/A			|  |
|set-sleep	|Enters the specified sleep mode.			|emAfMfglibSleepTest	|mode	|INT8U			|Available modes: 0-5*|
|enter-bl	|Enters the bootloader mode.				vemAfMfglibEnterBootloader	|N/A	|N/A	|	|
|per-test	|Sends specified # of packets at  to all devices on the same network set to receive;PER is displayed on the receiving device	|emAfMfglibPERtest	|packets	|INT16U	|Number of packets to send|
|	|	|	|interval	|INT16U	|Interval in milliseconds between packet transmissions|
|contPack-start	|Starts sending continuous packets at a 1000 millisecond interval	|emAfMfglibContinuousPacket	|N/A	|N/A	|	|
|contPack-stop	|Stops sending continuous packets	|emAfMfglibStopContinuous	|N/A	|N/A	|	|
|receive-mode	|Sets the print mode for incoming packets	|emAfMfglibReceiveMode	|mode	|INT8	|Packet receive mode 0-2*|	|
|receivePER-start	|Sets up receive mode for PER test by clearing packets and setting # of incoming packets	|emAfMfglibReceiveStart	|expected	|INT16U	|Number of expected packets|
|receivePER-stop	|Stops receive mode and prints out PER data	|emAfMfglibReceiveStop |	N/A	|N/A	|	|
|clear-tx-packets	|Clears the packet Counter(number of transmitted packets)|emAfMfglibClearPacketCounter	|N/A	|N/A	|	|
|get-tx-packets	|Prints packet Counter(number of transmitted packets)	|emAfMfglibGetPackets	|N/A	|N/A	|	|

set-sleep modes
- 0: Everything is active and running.In practice this mode is not used, but it is defined for completeness of information.
-  1: Only the CPU is idled. The rest of the chip continues running normally. The chip will wake from any interrupt. 
- 2: The sleep timer clock sources remain running. The RC is always running and the 32kHz XTAL depends on the board header. Wakeup is possible from both GPIO and the sleep timer. System time is maintained.
- 3: THe sleep timer clock sources remain running. The RC is always running and the 32kHz XTAL depends on the board header. Wakeup is possible from the only GPIO. System time is maintained. NOTE:This mode is not available on EM2XX chips.
- 4: The sleep timer clock sources(both RC and XTAL ) are turned off. Wakeup is possible from onl GPIO. System time is lost.
- 5: This maps to EM4 Hibernate on the EFM32/EFR32 devices. TAM is not retained in SLEEPMODE_HIBERNATE so waking up from this sleepmode will behave like a reset. NOTE: This mode is only available on EFM32/EFR32.

receive-mode modes
- 0: no print out
- 1: confirmation printout
- 2: printout with data and messages

## 2. Gecko SDK versions ##

 Gecko SDK Suite 4.0

## 3. Hardware Required ##

- Wireless Starter Kit Main Board (BRD4001)
- Any EFR32 radio board. A EFR32MG21 2.4 GHz 10 dBm Radio Board (BRD4181A) is needed to create the project from the .sls file. 
- For the PER test, a set of two boards are required in order for one to act as the receiver and one as the transmitter. 


## 4. Connections Required ##

For this project, connect a radio board to a mainboard, and connect to computer through USB. 

For example:
Connect the BRD4181A radio board to the BRD4001 WSTK mainboard.

Connect the BRD4001 WSTK mainboard to the PC through the USB.

Refer to QSG 180 for a more detailed description. https://www.silabs.com/documents/public/quick-start-guides/qsg180-zigbee-emberznet-7x-quick-start-guide.pdf 

## 5. Software Setup ##

The simplest way to create this project is to import the .sls file. Pull or download the file to your computer. Then in Simplicity Studio, go to File --> import and open the file. Build the file and then flash to your device. Note that this will only work if you have the same hardware. If you want to make changes and need to regenerate the .isc, copy the code in the manufacturing-library-cli.c and sl_cli_command_table.c files first. The following steps describe how to extend mfglib capabilities in an existing project.

1. Create a "Z3Switch" example for specific part number. In Simplicity Studio, go to new→ Silicon Labs Project Wizard...
![zigbee](doc/CreateZ3Switch.png)

2. Install mfglib related components through SOFTWARE COMPONENTS tab under .slcp file. Typing "manufaturing" on the search bar in the upper right corner can filter out the software components.
![zigbee](doc/InstallComponents.png)

	- Zigbee → Utility → Manufacturing library

	- Zigbee → Utility → Manufacturing Library CLI

	Save the .slcp file, Simplicity Studio automatically update the required project files when you make changes to the project configuration

3. Add additional codes to extend manufacturing library plugin

	```Note: When modifying the shared file in the SDK, it will prompt up a warning tab to indicate that a shared file is being modified. Please select "Make a Copy" to prevent these file are changed in SDK.```  
	![zigbee](doc/warning.png)

- manufacturing-library-cli-soc.c  
	(project_name/gecko_sdk_4.x/protocol/zigbee/app/framework/plugin/manufacturing-library-cli)

	Add the following code after the includes. This block of code has includes, defines, and variables.  
	The "..." indicates that there is additional code between the snippets. To get all the code, please copy it from the manufacturing-library-cli-soc.c file.
	```C
	////MFG UPDATED CODE START-------------------------------------------------------
	#include "rail.h"

	uint8_t txData[504] = { 0x0F, 0x0E, 0x11, 0x22, 0x33, 0x44, 0x55, 0x66, 0x77,
		0x88, 0x99, 0xAA, 0xBB, 0xCC, 0xDD, 0xEE, };
	uint16_t txDataLen = 16;
	// Data Management
	RAIL_DataConfig_t railDataConfig = { .txSource = TX_PACKET_DATA, .rxSource =
		RX_PACKET_DATA, .txMethod = PACKET_MODE, .rxMethod = PACKET_MODE, };

	uint32_t rxOverflowDelay = 10 * 1000000; // 10 seconds
	RAIL_CsmaConfig_t *csmaConfig = NULL;

	extern RAIL_Handle_t emPhyRailHandle;

	...

	void halSleep(SleepModes sleepMode);
	void halInternalSleep(SleepModes sleepMode);

	sl_zigbee_event_t packetSend;
	void packetSendHandler(void);
	////MFG UPDATED CODE END-------------------------------------------------------
	```  

	Event configuration functionality is no longer in the Simplicity Studio interface in SDK v7.x, but now is done in the Zigbee stack event system, by initializing the events in code. 

	In this example, the custom event is initialized in manufacturing-library-cli-soc.c file. Add the following code in emAfPluginManufacturingLibraryCliInitCallback function.

	```C
	void emAfPluginManufacturingLibraryCliInitCallback(SLXU_INIT_ARG)
	{	

  	... 

	////MFG UPDATED CODE START -------------------------------------------------------
	slxu_zigbee_event_init(&packetSend, packetSendHandler);
	////MFG UPDATED CODE START -------------------------------------------------------
	}
	```  

	Copy paste this function over the existing mfglibRxHandler function.
	```C
	//MFG UPDATED CODE START-------------------------------------------------------
	static void mfglibRxHandler(uint8_t * packet, uint8_t linkQuality, int8_t rssi) {

	// This increments the total packets for the whole mfglib session
	// this starts when mfglibStart is called and stops when mfglibEnd
	// is called.
	// additional code for emAfMfglibreceivePER

	mfgTotalPacketCounter++;
	mfgCurrentPacketCounter++;
	if (MODE1) {
		emberAfCorePrintln("Packet Received");
	}
	if (MODE2) {
		emberAfCorePrintln(
				"Received message: %s, link Quality: %u, RSSI: %d",
				packet + 5, linkQuality, rssi);

	}

	// If this is the first packet of a transmit group then save the information
	// of the current packet. Don't do this for every packet, just the first one.
		if (!inReceivedStream) {
		inReceivedStream = TRUE;
		mfgCurrentPacketCounter = 1;
		savedRssi = rssi;
		savedLinkQuality = linkQuality;
		savedPktLength = *packet;
		MEMMOVE(savedPkt, (packet + 1), savedPktLength);
		}
	}
	//MFG UPDATED CODE END-------------------------------------------------------
	```

	Add all the additional functions to the bottom of the file.   
	Since there are several hundred lines of code, it is not all included here. Please get the rest of the code from the bottom of the manufacturing-library-cli-soc.c file.
	```C
	//MFG UPDATED CODE START-------------------------------------------------------

	//Send set number of packets(set from argument) with message indicating the number sent
	void emAfMfglibPERTest(sl_cli_command_arg_t *arguments) {

	uint16_t numPackets = sl_cli_get_argument_uint16(arguments, 0);
	uint16_t interval = sl_cli_get_argument_uint16(arguments, 1);

	...

	void emAfMfglibEnterBootloader() {

	halInternalSysReset(RESET_BOOTLOADER_BOOTLOAD);
	}

	//MFG UPDATED CODE END-------------------------------------------------------
	```


- sl_cli_command_table.c  
	(project_name/autogen)

	Add the following function declaration for extension manufaturing library.
	```C
	////MFG UPDATED CODE START-------------------------------------------------------
	void emAfMfglibClearPackets(sl_cli_command_arg_t *arguments);
	void emAfMfglibSetPower(sl_cli_command_arg_t *arguments);
	void emAfMfglibGetPower(sl_cli_command_arg_t *arguments);
	void emAfMfglibSetCcaThresholdReg(sl_cli_command_arg_t *arguments);
	void emAfMfglibGetCcaThresholdReg(sl_cli_command_arg_t *arguments);
	void emAfMfglibGetCtuneValueReg(sl_cli_command_arg_t *arguments);
	void emAfMfglibSetCtuneValueReg(sl_cli_command_arg_t *arguments);
	void emAfMfglibGetCcaThresholdTok(sl_cli_command_arg_t *arguments);
	void emAfMfglibSetCcaThresholdTok(sl_cli_command_arg_t *arguments);
	void emAfMfglibGetCtuneValueTok(sl_cli_command_arg_t *arguments);
	void emAfMfglibSetCtuneValueTok(sl_cli_command_arg_t *arguments);
	void emAfMfglibSetGpio(sl_cli_command_arg_t *arguments);
	void emAfMfglibGetGpio(sl_cli_command_arg_t *arguments);
	void emAfMfglibGpioHelp(sl_cli_command_arg_t *arguments);
	void emAfMfglibTokDump(sl_cli_command_arg_t *arguments);
	void emAfMfglibSleepTest(sl_cli_command_arg_t *arguments);
	void emAfMfglibEnterBootloader(sl_cli_command_arg_t *arguments);
	void emAfMfglibPERTest(sl_cli_command_arg_t *arguments);
	void emAfMfglibContinuousPacket(sl_cli_command_arg_t *arguments);
	void emAfMfglibStopContinuous(sl_cli_command_arg_t *arguments);
	void emAfMfglibReceiveMode(sl_cli_command_arg_t *arguments);
	void emAfMfglibReceiveStart(sl_cli_command_arg_t *arguments);
	void emAfMfglibReceiveStop(sl_cli_command_arg_t *arguments);
	void emAfMfglibClearPacketCounter(sl_cli_command_arg_t *arguments);
	void emAfMfglibGetPackets(sl_cli_command_arg_t *arguments);
	////MFG UPDATED CODE END-------------------------------------------------------
	```

	Add the following command structs for extension manufaturing library. please get the complete code from sl_cli_command_table.c file.
	```C
	////MFG UPDATED CODE START-------------------------------------------------------

	static const sl_cli_command_info_t cli_cmd_mfglib_clear_hyphen_rx_hyphen_packets = \
	SL_CLI_COMMAND(emAfMfglibClearPackets,
					"reset the number of received packets to 0",
					"",
					{SL_CLI_ARG_END, });

	static const sl_cli_command_info_t cli_cmd_mfglib_set_hyphen_deci_hyphen_dbm = \
	SL_CLI_COMMAND(emAfMfglibSetPower,
					"Sets the power of radio transmissions(deci-dBm)",
					"The power level" SL_CLI_UNIT_SEPARATOR,
					{SL_CLI_ARG_INT32, SL_CLI_ARG_END, });

	...

	static const sl_cli_command_info_t cli_cmd_mfglib_get_hyphen_tx_hyphen_packets = \
	SL_CLI_COMMAND(emAfMfglibGetPackets,
					"Prints packet Counter(number of transmitted packets)",
					"",
					{SL_CLI_ARG_END, });

	////MFG UPDATED CODE END-------------------------------------------------------
	```

	Add the following CLI defintion for extension manufaturing library to the existing mfglib_group_table.
	```C
	////MFG UPDATED CODE START-------------------------------------------------------
	{ "clear-rx-packets", &cli_cmd_mfglib_clear_hyphen_rx_hyphen_packets, false },
	{ "set-deci-dbm", &cli_cmd_mfglib_set_hyphen_deci_hyphen_dbm, false },
	{ "get-deci-dbm", &cli_cmd_mfglib_get_hyphen_deci_hyphen_dbm, false },
	{ "set-cca", &cli_cmd_mfglib_set_hyphen_cca, false },
	{ "get-cca", &cli_cmd_mfglib_get_hyphen_cca, false },
	{ "get-ctune-reg", &cli_cmd_mfglib_get_hyphen_ctune_hyphen_reg, false },
	{ "set-ctune-reg", &cli_cmd_mfglib_set_hyphen_ctune_hyphen_reg, false },
	{ "get-cca-tok", &cli_cmd_mfglib_get_hyphen_cca_hyphen_tok, false },
	{ "set-cca-tok", &cli_cmd_mfglib_set_hyphen_cca_hyphen_tok, false },
	{ "get-ctune-tok", &cli_cmd_mfglib_get_hyphen_ctune_hyphen_tok, false },
	{ "set-ctune-tok", &cli_cmd_mfglib_set_hyphen_ctune_hyphen_tok, false },
	{ "set-gpio", &cli_cmd_mfglib_set_hyphen_gpio, false },
	{ "get-gpio", &cli_cmd_mfglib_get_hyphen_gpio, false },
	{ "gpio-help", &cli_cmd_mfglib_gpio_hyphen_help, false },
	{ "tok-dump", &cli_cmd_mfglib_tok_hyphen_dump, false },
	{ "set-sleep", &cli_cmd_mfglib_set_hyphen_sleep, false },
	{ "enter-bl", &cli_cmd_mfglib_enter_hyphen_bl, false },
	{ "per-test", &cli_cmd_mfglib_per_hyphen_test, false },
	{ "contPack-start", &cli_cmd_mfglib_contPack_hyphen_start, false },
	{ "contPack-stop", &cli_cmd_mfglib_contPack_hyphen_stop, false },
	{ "receive-mode", &cli_cmd_mfglib_receive_hyphen_mode, false },
	{ "receivePER-start", &cli_cmd_mfglib_receivePER_hyphen_start, false },
	{ "receivePER-stop", &cli_cmd_mfglib_receivePER_hyphen_stop, false },
	{ "clear-tx-packets", &cli_cmd_mfglib_clear_hyphen_tx_hyphen_packets, false },
	{ "get-tx-packets", &cli_cmd_mfglib_get_hyphen_tx_hyphen_packets, false },
	////MFG UPDATED CODE END-------------------------------------------------------
	```

Finally, build the project. All functions should be ready to use.
Flash project to preferred board and go to network analyzer to use functions.
To flash, in the Simplicity IDE perspective go to project_name and open binaries. Left click project_name.s37 and select "Flash to Device". Pick a board and then choose "Program".

## 6. Examples ##

### 6.1 How to change packet print outs ###

The "plugin mfglib receive-mode <int8_t>" allows the user to get different print outs for each packet. Some packets have messages attached, often numbers. Some do not or have random data, which will show up as scrambled characters. 
Modes:
	Mode 0: no printout
	Mode 1: "Packet Received" printed for each received packet
	Mode 2: "Received message: <string>:, link Quality: <int>, RSSI: <int>"
	
Example: 
	
![zigbee](doc/ReceiveMode.png)
	
### 6.2 How to navigate packet counters ###

Because the code can set up your board to both receive and send packets, there is a transmitting (tx) packet counter and a receiving (rx) packet counter. 

When your board is set to receive packets, use the following commands:

```C
plugin mfglib status //Will print out information, including the number of received packets
plugin mfglib clear-rx-packets //Will clear the received packet Counter
```
When it is sent to send packets, use the following commands:

```C
plugin mfglib get-tx-packets //Prints out the number of transmitted packets(packet Counter)
Plugin mfglib clear-tx-packets //Clears the transmitted packet Counter
```

### 6.3 How to use the continuous packet test ###

Flash project to two boards. One will be the transmitter, one will be the receiver. 

On the receiver board, connect and launch console. Go to serial 1 tab. enter the following commands :

```C
network leave
plugin mfglib start 1 //this command is needed to start receiving packets
plugin mfglib set-channel 12
Plugin mfglib set-power 18 1
plugin mfglib receive-mode 0 //see 6.1 for details on this command
```

Flash to second board and enter following commands into serial 1 tab, enter the following commands will start the continous packet test:

```C
network leave //when testing mfglib, it is important that the device is not on a network. 
plugin mfglib start 0 //this command is needed to start sending packets
plugin mfglib set-channel 12 //channels can be set to 11-26. The default channel is 11.
plugin mfglib set-power 18 1  //sets power and mode. NOTE: this is different from set-deci-dbm
plugin mfglib contPack-start //this commands start automatically sending a packet per second. MY_DELAY_IN_MS is defined in the code and can be set to a different interval
```

Once you are done with the continuous packet test, enter this command in the transmitter console to stop it:

```C
plugin mfglib contPack-stop
```


See 6.2 for details on clearing and getting packet counts(both on transmitter and receiver sides)

To see more information, right click on the board and click “start capture” 
![zigbee](doc/NAcapture.png)

### 6.4 How to use the PER test ###

Flash project to two boards. One will be the transmitter, one will be the receiver. 

On the receiver board, connect and launch console. Go to serial 1 tab. enter the following commands:

```C
network leave //when testing mfglib, it is important that the device is not on a network. 
plugin mfglib start 1 //this command is needed to start receiving packets
plugin mfglib set-channel 12 //channels can be set to 11-26. The default channel is 11.
plugin mfglib set-power 18 1  //sets power and mode. NOTE: this is different from set-deci-dbm
plugin mfglib receive-mode 0 //see 6.1 for details on this command
plugin mfglib receivePER-start 100
```
The parameter to receivePER-start is how many packets you would like to send. In the example, 100 are sent. This set of commands sets up the receiver to accurately count the number of packets.

On the transmitter board, connect and launch console. Go to serial 1 tab. enter the following commands:

```C
network leave
plugin mfglib start 0 //this command is needed to start sending packets
plugin mfglib set-channel 12
plugin mfglib set-power 18 1
plugin mfglib per-test 100 1500
```
There are two arguments to per-test, the first one indicates how many packets should be sent. This must match the value passed into receivePER-start for accurate results. The second parameter is the delay between packets in microseconds. 

See 6.2 for details on clearing and getting packet counts(both on transmitter and receiver sides)

In the receiver console, you will see printouts for each of the received packets. Once no new packets are being received (this should happen quickly, since the delay is in microseconds), use the following command:

```C
plugin mfglib receivePER-stop //will print out the expected # of packets, received # of packets, and PER.
```

To see more information, right click on the board and click “start capture” 
![zigbee](doc/NAcapture.png)


### 6.5 How to configure CCA Threshold and CTUNE ###

Both CCA Threshold and CTUNE can be configured temporarily, or by setting their tokens. 

During testing, you may want to frequently change the token. To set temporarily, use the following commands:

```C
plugin mfglib set-cca -75 //will set the temporary CCA Threshold value to -75
plugin mfglib get-cca //will show what the current CCA Threshold is

plugin mfglib set-ctune-reg 135 //will set the temporary Ctune value to 390
plugin mfglib get-ctune-reg //will show what the current Ctune value is

```

To set these values more permanently, you can set the token using the following commands:

```C
plugin mfglib set-cca-tok -75 //will set the CCA Threshold token to -75
plugin mfglib get-cca-tok //will show what the CCA Threshold token value is

plugin mfglib set-ctune-tok 135 //will set the Ctune token to 390
plugin mfglib get-ctune-tok //will show what the Ctune token value is

```

Note that you cannot set tokens thats have already been set. You will get an error if you try to set a token that already has a value. In order to wipe the tokens and get a new value, use the following commands in commander:

```C
commander readmem --range 0x0FE00000:+1024 //this will read out the token data, so you can see the token values you may be deleting

commander device pageerase --range 0x0FE00000:+1024 //this will erase all tokens
```
NOTE: This will wipe ALL the tokens, not just ctune or cca threshold.
More on tokens and commander can be found here: https://www.silabs.com/documents/public/user-guides/ug162-simplicity-commander-reference-guide.pdf

### 6.6 How to configure GPIO ###  

The manufacturing library extension provides function to configure the GPIO for test. By entering "plugin mfgilb gpio-help", detailed information of how to set GPIO is shown.  
![zigbee](doc/gpiohelp.png)  

The commands below show an example about setting PB01 to high, which is conected to the GREEN LED on xg21.
```C
plugin mfglib gpio-help //get information of GPIO command inputs.

plugin mfglib set-gpio 1 1 4 1 // will set PB01 to gpioModePushPull mode with High value
plugin mfglib get-gpio 1 1 //will get PB01 pin mode
```

## 7. Additional Information ##

### 7.1 Warnings ###

This code is not validated for certification testing over the air; it is intended for informal pre-certification manufacturing testing only.

