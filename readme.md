# c64-v9958-card

**Copyright (c) 2024 Vossi - v 1.0**
**Basic-Cartridge (c) 2024 Vossi**
**www.mos6502.net**

## License
This work is licensed under a Creative Commons Attribution-ShareAlike 4.0
International License. See [https://creativecommons.org/licenses/by-sa/4.0/](https://creativecommons.org/licenses/by-sa/4.0/).

:thumbsup: final pcb version 1.0
C64 (C128) color-graphics-card with the Yamaha V9958 (V9938) VDP and 128kB dedicated video ram.

**[Schematic](https://github.com/vossi1/c64-v9958-card/blob/master/doc/schematics.png)**

**[Parts](https://github.com/vossi1/c64-v9958-card/blob/master/doc/partlist.txt)**

**description:**

    This is a color-graphics-card that plugs in the expansion slot of the C64 / C128.
    It is based on the Yamaha MSX2+ video display processor (VDP) V9958 (MSX2: V9938).
    Both VDP's are backward compatible to the TMS9918/9928 used in many old home computers.
    The V9958 has some additional features compared to the V9938 like vertical soft scrolling
    and some 19k color modes.

    The VDP has a RGB output programmable for PAL or NTSC. You need an analog RGB monitor
    like the 1084 or a scart TV. The V9958-card has 128kB dedicated RAM - only reachable
    through the VDP-ports. It has screen resolutions from 256x192 up to 512x212 / 512x424i
    and 16/256/19k colors with 32 sprites, soft scrolling and hardware commands like: fill,
    line, copy. Screen modes up to 5 need only 64kB vram, mode 6 and 7 need 128kB ram,
    because of faster bank interleave access.

![V9958-card photo](https://github.com/vossi1/c64-v9958-card/blob/master/pictures/cart_front.jpg)

**details:**

    I placed the V9958 at $DF00 ($DE00) (i/o 1/2), so the write-ports are $DF00-$DF03.
    I had some trouble because the 6502 does a read access in cycle 5 of the STA(ZP),Y
    instruction before it writes. This increased sometimes the address pointer of the VDP.
    Thats why I splittet the read and write access ports. The read access ports are $DF04-$DF05.
    The programmable logic chip (GAL) does the splitting and outputs the CSR/CSW signals
    for the VDP. I also added a 32kB cartridge eprom socket for two switchable 8 or 16k images.
    I converted the complete cbm2-font to 6x8 matrix because the VDP has only 256/512 pixels width.
    There ia a GAL file for two autostart C64 images and another for one C64 and a C128 image.
    With a modification in the GAL it's a also possible to use the cart as 2x16kB with bank
    switching via $DF08 (untested).
    
![V9958-card back photo](https://github.com/vossi1/c64-v9958-card/blob/master/pictures/cart_back.jpg)

**assembling hints:**

    I soldered all the PLCC and SOJ sockets with solder paste and hot air.
    After soldering I checked if all pins are really fixed and no bridge exists!
    With two solder jumper on the back you can select 27/29c256 or 28c256 eprom type.
    For the DRAM you have also the option to use a THT cap instead SMD. 

**RGB output:**

    The RGBS outputs are amplified with emitter followers, as specified by Yamaha.
    The V9958 has 1Vpp output level with 1k GND-resistors. With the 22R resistors you get
    a perfect 0,7Vpp RGB signal. Sync has TTL-level (use 68R or 75R output resistor).
    I decoupled the RGBS outputs with tantal/mlcc capacitors of 220 uF/6.3V (Sync 100uF).
    You can use tantal size B or MLCC caps fot the decoupling.

**comments:**

    For mode 6 and 7 it needs 128kB because it uses bank interleave access to get the speed!
    The card uses one dram for bank0 and one for bank 1.
    I tried everything to use only one chip - but without success. I tried to use the lower
    byte for cas0 and the upper byte for cas1.
    I also tried to use cas0/1 to switch an address line to use 128kB in one chip - no success.
    So I decided to use two common drams - easy to find in china or on old 1/2 MB pc-vga-cards.
    In the C128DCR I had some false pixels in the graphics (especially in 2MHz mode).
    My CBM2-card runs fine at 2MHz - so it's not a speed problem. I think the longer traces, or
    the C128 design is the reason. Maybe it helps to shield the cartridge ? 

The pcb fits exactly in a Commodore-cartridge-case:
![V9958-card case](https://github.com/vossi1/c64-v9958-card/blob/master/pictures/cart_cable.jpg)

**examples: helloworld.a - demo/starwars.a**

    Use the great ACME assembler to assemble the files. It is available for OSX, linux, windofs.
    Helloworld is small "one file" testcode for the card. It uses some 6502 and vdp macros.
    It has a 91 character-font (in hex bytes) and the c64-color-palette :wink:
    Starwars is a bigger demo in graphics mode 7. I converted the picture with my BMP2RGB8 tool.
    Both tests/demos were selectable for C64 / C128.

BASIC:
![V9958-basic-demo](https://github.com/vossi1/c64-v9958-card/blob/master/pictures/basic-demo.jpg)

**basic cartridge c64 / vdemo**

    The binary is for an 8KB eprom at $8000 (in the 32kB-PLCC eprom/eeprom).
    This cartridge runs only on a C64 or C128 in 64 mode.
    It adds many great new statements. All new graphics commands are shown in txt file.
    My extension supports mode 6 with 256x212 pixel, 256 fixed colors and mode 7 with 512x212
    pixel and 16 palette colors out of 512. Both modes have 32 sprites with 16x16 size!
    PAL/NTSC is selectable with the MODE command. CLEAR, LINE, FRAME, BLOCK, VCOPY statements
    and the scrolling subroutine use the very fast internal V9958 commands.
    The upper left corner is 0,0 because the sprites also use these orientation.
    RAM $CE00-CFFF is used for buffering sprite colors for each of the 64 pattern!
    The V9958 stores the sprite colorlines for each sprite and not for each pattern - But my
    extension does that ;) If you select one of the 64 pattern for a sprite, SPRITE also copies
    the colorlines from my buffer to the colortable for this specific sprite.
    It's also possible to override the color with SCOLOR temporary - but only unicolor.
    You can convert 24 bit RGB BMP-pictures with up to 256x212 (only even x values) to 8bit RGB
    for the IMAGE command with my BMP2RGB8 tool here on git.
    The Basic program v64demox.prg shows how to use the new statements in mode 6 or 7.
    In the tiny draw-tool v64plotx.prg you also see use of the new TEST-function in mode 6 or 7.

    If you find a bug or have a question? -> leave me a note...
