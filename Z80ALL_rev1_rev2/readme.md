# Rev1, Rev2 Z80ALL
Z80ALL is my first attempt to build a standalone CP/M system. It is the combination of two previous designs, ZRCC and VGARC. The goal is an economical Z80 SBC with VGA and PS2 keyboard on a 4“x4” 2-layer pc board.  Rev1 is same as rev2 except rev1 is 2-layer pc board while rev2 is 4-layer pc board.

![rev1top](z80all_rev1_topview.jpg)

### Features
- Z80 overclocked to 25.175MHz
- 128K RAM in 4 32-K banks
- 4K dual port video RAM with user programmable font table.
- VGA monochrome interface, 64 columns X 48 rows
- EPM7128S CPLD with the following features
  - Small ROM to bootstrap from CF disk
  - VGA timing circuit
  - Serial port for hardware/software development
  - Memory bank select logic
  - Decoding logic for compact flash
- IDE44 interface for compact flash drive
- CP/M ready
- PS2 keyboard interface
- 3 RC2014 expansion bus
- Optional USB-serial connector
- 102mm X 102mm 2-layer pc board
- Nominal power consumption of 5V 300mA

![rev12topanno](z80all_rev1_topview_annotated.jpg)  

### Theory of Operation
Z80ALL boots through the 32-byte ROM embedded in the CPLD which loads and executes code on compact flash's Master Boot Block; which, in turn, loads and executes a monitor located in Track 0 of compact flash; thus the CF disk serves as the traditional EPROM loading code into RAM and executing in RAM.

The VGA interface is through a 4Kx8 dual port RAM. One side of the dual port RAM is read/write accessible by Z80 as 4K I/O space. 3K of the I/O space maps to each character of the 64×48 display; the top 1K is font lookup table for characters 0x0-0x7F. Z80 can read/write to its side of dual port RAM anytime without affecting video display quality. The other side of the dual port RAM is read only accessible by VGA timing circuit in CPLD. It reads each character and looks up corresponding font and output the pixel representation of the character on RGB output. This is a monochrome display.

### Design Information
- [Schematic](z80all_rev1_rev2_scm.pdf)
- [Rev1 Gerber photoplots](z80all_r1_gerber.zip), it is obsolete, replaced by rev 2 photoplots
- [Rev2 Gerber photoplots](z80all_rev2_gerber.zip) 11/17/23, replacing rev1 photoplots <- same design as rev1 but it is now a 4-layer PCB.
- [CPLD design](z80all_rev1pcb_cpld_design_file_64column_48line_no_ps2.zip) ← CPLD design is still evolving. This is an interim working design but without PS2 keyboard interface
- [Bill of Materials](z80all_rev1_rev2_bom.pdf)
- Memory and I/O Map

### Software
- Bootstrap ROM in CPLD, this 64-byte bootstrap can boot from serial or CF disk
- Simple ROM bootstrap, this 32-byte bootstrap always boot from CF disk
- Serial Loader resides in compact flash's Master Boot Record
- Z80ALL monitor
- SCMonitor+StarTrek This is Steve Cousin's SCMonitor ported to Z80ALL. This is Steve Cousin's homepage. As an extra bonus, it includes the StarTrek program in BASIC. To install SCMonitor+StarTrek, send scmonitor_startrek_z80all.hex to Z80ALL and type 'c1' to install it in track 0 of CF disk. Once it is installed, type 'b1' to load and run SCMonitor. To run Startrek in BASIC, type 'wbasic', then 'run'. Have fun!
- CP/M 2.2 BIOS/CCP/BDOS
- CP/M3 Loader. This is loader for CP/M3. It expects CPM3.SYS in drive A of the CF disk. To install it, send CPM3LDR.HEX to Z80ALL and type 'c3' to install it in track 0 of CF disk. Once it is installed, type 'b3' to boot CP/M3.
- CPM3ALL. This is the CP/M 3 distribution files. Use unarj.com to decompress it.
- CPM3 banked BIOS source code, assembled with zmac
- CF image file contains CP/M2.2, CP/M3, HiTech C, Zork, SCMonitor. Unzip to z80all_64MB_cpm2_cpm3_scmon_htc_zork.img and use Win32DiskImager to copy the image file to a 64MB or larger CF disk.

### Demo
Game of Life
This is a video of Conway's Game of Life running on Z80ALL. The software itself is only 500 bytes, it takes advantage of the read/write capability of the video memory and perform required calculations directly on the video memory. The dual port memory impose no restriction on Z80's access and the display quality is not at all affected by the numerous Z80 read/write access. http://https://youtu.be/qt8Mx9dZJj0

Scroll Test
The VGA circuit in Z80ALL is simple; it does not have hardware scroll function. To scroll a line, the entire screen needs to be read and re-written with one line offset. This may seems a slow process, but because there are only 3K characters in consecutive I/O addresses, the operation can be done with INDR and OTDR instructions. https://www.youtube.com/watch?v=Dg1U_u5plDk
