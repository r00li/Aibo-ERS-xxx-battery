# Sony Aibo ERS-XXX battery

**A replacement battery pack for older Sony Aibo robot dogs - ERS-3xx/ERS-7/ERS-2xx.**

![Main image](https://github.com/r00li/Aibo-ERS-xxx-battery/blob/main/Misc/image_full.jpg?raw=true)

## Tell me more?

In the early 2000s Sony released a series of robot dogs under the name Aibo ([Wiki](https://en.wikipedia.org/wiki/AIBO)). In the period until 2006 several generations were produced (ERS-11X, ERS-2XX, ERS-31X and ERS-7),  then in 2006 the Aibo robots were discontinued until they were brought back in 2018 with ERS-1000. Since support for the original series of Aibos has long been discontinued (some being over 20 years old by this point) owners were left without spare parts for these robots. One of the most problematic parts is the proprietary lithium-ion battery pack.

The main aim of this project is to provide instructions for making replacement battery packs for the original series of Aibos. Contained here is a redesigned battery protection/communication PCB created using modern readily available components based around the [Maxim MAX17320](https://www.maximintegrated.com/en/products/power/battery-management/MAX17320.html) battery fuel gauge.

Currently supported and tested are ERS-3xx, ERS-7 (since they use the same battery pack) and ERS-2xx Aibos. The older ERS-11X Aibos use battery packs using HDQ communication standard (while the newer ones use SMBUS) so they are not supported for now.
  

## Building one yourself

In theory this should be pretty simple. Download the gerber files and get the PCBs made, buy the necessary components and populate the board, program the firmware into the fuel gauge, assemble the board into a case and your Aibo has a new battery. BUT... Keep in mind that making battery packs involves dealing with lithium batteries. So if you are not sure what you are doing or are not sure what the proper procedures are for using raw lithium cells - **do not try this at home.** If you are sure that you know what you are doing... read on.

### Hardware

![Schematic](https://github.com/r00li/Aibo-ERS-xxx-battery/blob/main/Misc/schematic_preview.png?raw=true)

In order to build this you will need the following (plus some more or less specialized tools - soldering station, spot welder, ...):

- Assembled PCB (gerber files supported by common PCB manufacturers like JLCPCB are contained inside of the Gerbers and assembly folder)
- Components listed inside of the BOM file (you can find it in the Gerbers and assembly folder)
- Two lithium-ion cells of your choice - either 18650 or 18500. Be sure to use something that has at least 5A continuous discharge current and I recommend using cells that are around 2000mAh.
  - Using 18650 means that you will have to spot weld them since there is not enough space in the case to fit any removable attachment methods. But they offer higher current rating and are more appropriate for this task
  - Using 18500 gives you an option of making the cells easily removable. In this case you will need a few standard AAA battery contacts to assemble into the case. Note that cells with high enough capacity and current are harder to find.
- Battery case that will fit inside of an Aibo (see below)
- A way to program the battery pack (see Firmware section)

Note that ERS-2xx and ERS-3xx boards are almost identical so the instructions are the same. The only real difference is the placement of the battery connector and a few sorrounding components.

Assemble the board according to the BOM/assembly guide (found in Misc folder). Note that this is using primarily 0402 sized SMD components so be sure that your soldering skills are appropriate. For ordering parts use the ones written in the BOM - there are a few changes as to what is written in the schematic although both variants should work (when buying MAX17320 make sure that you get the I2C version). When assembling the board install the battery connector last. Note that this one is assembled slightly offset from the board, so it is recommended that you put the board into the case  together with the connector and only then solder the connector. Note that connector is mounted on the other side than the rest of the components.

You are now ready to connect your cells. We are using 2S (series) configuration here. Connect the B+ to positive terminal of the cells, connect B- to the negative and BM to the middle of the pack. After you are done installing the cells you need to jump start the fuel gauge. You can do that by placing it into the Aibo and connecting a charger for a second or by using a piece of wire and for a second connecting the B+ pin to the pin 1 of the battery connector (the one nearest the edge/BM pin). After this you can program the configuration into the fuel gauge.

Your assembled board should look something like this:

![Assembled board](https://github.com/r00li/Aibo-ERS-xxx-battery/blob/main/Misc/assembled_board.jpg?raw=true)

### Case

During the development of the project I got help with designing the case from Ebi. Without him this project would probably still be in development. Since the case design is his you will need to contact him in order to get the STL files. You can find more information regarding this on his home page:
https://ebisempire.com/AIBO-s/Downloads

### Firmware

By default MAX17320 comes with SBS (SMBUS) operation disabled. You need to program the chip with the correct values in order for it to work with the Aibo. This can be done in two ways - using any I2C capable device (computer, arduino, ...) by setting the correct values (the correct values for each register are located inside the .INI file inside the Firmware folder) manually - how to do this is written in the MAX17320 datasheet.

The easier option is using the [MAX17320 evaluation software](https://www.maximintegrated.com/en/design/software-description.html/swpart=SFW0010930H) (available for download for free from Maxim site linked). But this officially only works with the MAX17320 evaluation kit. However you can use any FTDI FT232H board (I used the Adafruit FT232H breakout board) if you flash the chip with the correct values that Maxim software will recognize. Those settings are written below. You can connect your FTDI board to your Windows computer, install the FTDI drivers and then program the chip using [FT_Prog utility](https://ftdichip.com/utilities/#ft_prog) (can also be found on FTDI website)

The correct settings are:

    VID: 0403
    PID: 6001
    Manufacturer name: FTDI
    Product Description: Dual RS232-HS A
    Serial number: A

After you have your board configured correctly you can connect the battery pack to your FTDI board using the schematic here: https://github.com/AndreasVan/ERA-7B1-Battery-Reader/blob/master/connection_scheme.jpg . Or TLDR:

    Pin 1 = Plus - NC
    Pin 2 = SMBus Clock - FTDI CLK
    Pin 3 = SMBus Data - FTDI - DATA
    Pin 4 = Enable - NC
    Pin 5 = Internal termistor - NC
    Pin 6 = Minus - FTDI GND
    
    * 4.7k Pull up resistors are needed on Data and Clk pins

You can now open the FTDI software and use it to program the board with the provided INI file inside the Firmware folder. After your board has been programmed you are good to go. You can disconnect the pack from the computer and charge it in the Aibo.

For more details on how to use the finished project you can look at the user manual for this battery: [User manual](https://github.com/r00li/Aibo-ERS-xxx-battery/blob/main/Manual/battery_instructions.pdf?raw=true)

Note that you may need to pull the battery out and re-insert it from the Aibo if it will not start up/play the sad melody. Additionally the battery pack may need 1 or 2 cycles before Aibo will show the correct charging pose. This is because the fuel gauge needs to calibrate itself to work correctly. After it has done that you shouldn't remove the cells any longer or the calibration data will be lost (the programming will stay however).

![Maxim programming software](https://github.com/r00li/Aibo-ERS-xxx-battery/blob/main/Misc/Capture_empty2.PNG?raw=true)

## Can I buy a ready to go battery pack?

I don't offer a ready to go battery packs. In order to get one of those please contact [Ebi](https://ebisempire.com). He buys the boards from me and builds them into working ready-to-go battery packs. 

## Help and support

As always... try reading the user manual for this project first: [User manual](https://github.com/r00li/Aibo-ERS-xxx-battery/blob/main/Manual/battery_instructions.pdf?raw=true)

If you need help with anything that is not covered by the manual, contact me through github (open an issue here). You can also check my website for other contact information: http://www.r00li.com .

## Special thanks

Special thanks go to the following people:
- [Ebi](https://ebisempire.com) - For designing the case for this project
- https://github.com/AndreasVan - for his battery reader project and wiring schematic/pinout
- https://github.com/lpollier - for reverse engineering the ERS-2xx battery pack and giving me the idea to actually start this project in the first place

## License

This work is licensed under the Creative Commons Attribution-NonCommercial-ShareAlike 4.0 International License. To view a copy of this license, visit http://creativecommons.org/licenses/by-nc-sa/4.0/ or send a letter to Creative Commons, PO Box 1866, Mountain View, CA 94042, USA.

![enter image description here](https://i.creativecommons.org/l/by-nc-sa/4.0/88x31.png)

