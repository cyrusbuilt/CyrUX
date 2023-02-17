# CyrUX
![Build Status](https://github.com/cyrusbuilt/CyrUX/actions/workflows/ci.yml/badge.svg)

UX Add-on card (VGA, Audio, and PS/2) for the SC500-series computer system.

## Features
- VGA Display output
- - SVGA 1280x768 B&W @ 50Hz
- - SVGA 1024x768 4 colors @ 75Hz
- - SVGA 800x600 8 colors @ 56Hz
- - PAL 720x576 16 colors @ 50Hz
- - VGA 640x480 16 colors @ 73Hz
- - VGA 640x480 16 colors @ 60Hz
- - VGA 640x350 16 colors @ 70Hz
- - VGA 512x384 64 colors @ 60Hz
- - VGA 400x300 64 colors @ 60Hz
- PS/2 Keyboard and Mouse
- Amplified Mono Audio Output (using standard 3.5mm stereo/mono jack)
- OSD configuration GUI with mouse support
- Multiple terminal color options

At the heart of this board is an [ESP32](https://learn.adafruit.com/huzzah32-esp32-breakout-board) and the firmware was built using [FabGL](http://www.fabglib.org/). In fact, the firmware is essentially a customized version of the AnsiTerminal demo application in the FabGL library package. This board was specifically designed to work with [Stephen Cousin's SC500-series modular computer system](https://smallcomputercentral.com/modular-z50bus-system-sc500-series/), but since it really only requires the +5V, GND, and serial RX and TX pins of the [Z50 Bus](http://linc.no/products/z50bus/), you *could* connect it to something else using something like female-to-whatever dupont wires or a 50-pin IDC ribbon cable.  Essentially, this is *terminal* card and, as such, doesn't use memory mapped I/O. But it is interrupt-driven, uses both cores of the ESP32 and operates at 115200 BAUD, so it is quite fast.

## Hardware
Schematics, BOM, and production gerbers can be found in the 'schematics' folder and were created using [EasyEDA](https://easyeda.com/).

## Software
This project was built using [PlatformIO](https://platformio.org/) and the sources can be found in the 'src' and 'include' folders. As mentioned above, the firwmare makes extensive use of FabGL. There are a number of terminal escape sequences you can use to access graphics/audio/ADC/GPIO/etc features. See [FabGL Documentation](http://www.fabglib.org/special_term_escapes.html) for details.

## Jumpers
Jumper JP1 (Enable Bus Power) should be jumpered by default. You can remove this jumper if you want to power the board from the 6-pin programming/debug header (H1) using something like an [FTDI cable](https://www.adafruit.com/product/70?gclid=Cj0KCQiA6LyfBhC3ARIsAG4gkF_bjI0ezGcmS23cLkyhw6B5iF6oedXxsI0ABbbVM_5l3eShE-9gH7waAlexEALw_wcB). This can be use for troubleshooting/debugging firmware issues without having the board plugged in to the SC500 backplane (standalone). Jumper JP3 (Enable RX) should also be jumpered by default. It may be useful to remove this jumper when flashing new firmware as I've had sporadic issues during the flashing process with the RX line connected (for reasons I have not yet identified). This jumper *may* be removed in a future revision.

## Building and Flashing the Firmware
The best way to do this is to leave the board unplugged from the SC500 backplane and then do the following:

1) Per the jumper documentation above, remove both jumpers.
2) Connect an FTDI cable (like the one linked above) to pin header H1 making sure to match the GND pin up and then plug the USB end in to your computer.
3) Make sure the monitor_port line in the platformio.ini is set to match the port for the FTDI cable on your machine.
4) Press both and hold both buttons on the ESP32 (see documentation for details) to put it in programming mode.
5) Enter the following command in your terminal at the root of the project folder:
```bash
> pio run --environment featheresp32 --target upload
```
6) Wait for the flashing process to complete, then disconnect the FTDI cable from the board.
7) Reinstall both jumpers.
8) Reinstall the board back into the backplane.
9) Power up the system and you're all set!

## Known Issues
When you first power on the system, you may see some jumbled output on the screen. This is because the host system and the CyrUX board are booting up at the same time and the serial connection will be out of sync at first. If this really bothers you, you can press the reset button on the host system to clear and re-initialize. If you want to see the CyrUX boot info screen, you can press F12 on your keyboard to access the OSD screen and click 'Reboot' to re-init the display. I may try to fix this in a future revision by holding the /RESET pin on the Z50 bus until CyrUX has a chance to finish booting first. However, the downside to this would be any time you reboot the display, you'd also reboot the host machine. Since this may not always be desirable behavior, it may be best to also add a jumper.