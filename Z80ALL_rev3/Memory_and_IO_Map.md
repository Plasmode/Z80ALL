# Z80ALL Memory and I/O Map for Standalone Z80ALL
### Memory Bank Register
Z80ALL has a 128K of RAM divided into 4 banks of 32K blocks:

- Z80's top 32K memory is always mapped to physical memory 0x8000-0xFFFF
- Z80's bottom 32K memory is mapped to one of four 32K blocks via the bank select register as follow:
- Bank select register is write only register located at I/O address 0x1F
- Bank select register is 0x3 at reset which maps the highest 32K RAM block to Z80's high 32K memory as well as Z80's low 32K memory.
- Bank select register may contains value from 0x0 to 0x3; 0x0 maps RAM's lowest 32K block to Z80's low 32K memory; 0x1 maps RAM's next lowest 32K block to Z80's low 32K memory, so on.
- Bank select register also enable/disable CPLD's boot ROM via data bit 7. CPLD boot ROM is enabled at reset. Writing '1' to data bit 7 will disable the CPLD boot ROM. Once CPLD boot ROM is disabled, it can not be enabled until next reset.
### Compact Flash interface
Compact flash interface are on I/O addresses 0x10 to 0x17

- 0x10 is CF data register
- 0x11 is CF error/feature register
- 0x12 is CF sector count register
- 0x13 is CF LBA bits 0-7
- 0x14 is CF LBA bits 8-15
- 0x15 is CF LBA bits 16-23
- 0x16 is CF LBA bits 24-27
- 0x17 is CF status/command register
### PS2 keyboard interface
PS2 registers are accessible on I/O addresses 0xF4 and 0xF5

- 0xF4 is PS2 input data register
- 0xF5 is PS2 status register and command register
Further details regarding 0xF5, command/status register. Bit 0 of status register indicates whether there is a valid keyboard input at PS2 data register such that PS2 status bit0=1 indicates valid data. When bit0 indicates a valid data in PS2 data register, reading PS2 data register will automatically clear bit 0 of the status register. Bit 6 of status register indicates whether 60Hz interrupt is enabled (bit6=1) or disabled (bit6=0, reset set bit 6=0). Bit 7 of status register indicates whether there is an active 60Hz interrupt (bit7=1). Write '1' to bit 6 of the command register will turn on 60Hz interrupt that asserts at the beginning of VGA's vertical blanking period. Write '1' to bit 7 of the command register will clear the current 60Hz interrupt but does not turn off subsequent 60Hz interrupt. Another word, if status read shows bit 7 is '1' indicating an active 60Hz interrupt being generated, writing '1' to command register will clear bit 7 and negate current interrupt but future interrupts will continue be generated at 60Hz rate.

### Video RAM Interface
Z80ALL has a 4K dual ported RAM located in I/O space 0x0 to 0xF. The content of RAM is both read and write accessible at any time, unfettered by the activity of video logic on the other port of the dual-port RAM. The full 4K I/O space is accessible using “IN A,(C)” and “OUT (C),A” instructions where reg C points to group of 4 lines and reg B points to 256 locations within the group of 4 lines. The following is allocation of video resources on the 4K RAM:

- regC=0x0 is first 4 lines where regB=0 is top left character of the display; and regB=63 (hex 0x3F) is end of the first line; regB=64 (hex 0x40) is beginning of 2nd line; regB=128 (hex 0x80) is beginning of 3rd line; and regB=192 (hex 0xC0) is beginning of 4th line.
- regC=0x1 is 2nd group of 4 lines, line 5 to line 8.
so on,
- regC=0xB is12th group of 4 lines, line 45 to line 48.
- regC=0xC is font lookup table corresponding to character values 0x0 to 0x1F. Each font is 8×8 requiring 8 bytes to represent a character. RegB=0 corresponds to top 8 pixels of 8×8 font; the most significant bit is the leftmost pixel and the least significant bit is the rightmost pixel. RegB=1 to 7 correspond to next seven 8-pixel array of a font. RegB=8-0xF is font table for character value 0x1, RegB=0x10-0x17 is font table for character value 0x2, so on.
- regC=0xD is font lookup table corresponding to character values 0x20 to 0x3F.
- regC=0xE is font lookup table corresponding to character values 0x40 to 0x5F.
- regC=0xF is font lookup table corresponding to character values 0x60 to 0x7F.
