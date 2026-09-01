# Z80ALL Memory and I/O Map (Under Construction!!)
Z80ALL has a 128K of RAM divided into 4 banks of 32K blocks:

- Z80's top 32K memory is always mapped to physical memory 0x8000-0xFFFF
- Z80's bottom 32K memory is mapped to one of four 32K blocks via the bank select register as follow:
- Bank select register is write only register located at I/O address 0x1F
- Bank select register is 0x3 at reset which maps the highest 32K RAM block to Z80's high 32K memory as well as Z80's low 32K memory.
- Bank select register may contains value from 0x0 to 0x3; 0x0 maps RAM's lowest 32K block to Z80's low 32K memory; 0x1 maps RAM's next lowest 32K block to Z80's low 32K memory, so on.
- Bank select register also enable/disable CPLD's boot ROM via data bit 7. CPLD boot ROM is enabled at reset. Writing '1' to data bit 7 will disable the CPLD boot ROM. Once CPLD boot ROM is disabled, it can not be enabled until next reset.
Z80ALL has a simple serial port operating at a fixed baud rate of 115200 N-8-1. The receive register has one buffer and transmitter is a bit-bang register. The following is I/O address of the serial port:

I/O map of compact flash interface:

I/O map of PS2 keyboard interface:

Z80ALL has a 4K dual ported RAM located in I/O space 0x20 to 0x2F. The content of RAM is both read and write accessible at any time, unfettered by the activity of video logic on the other port of the dual-port RAM. The full 4K I/O space is accessible using “IN A,(C)” and “OUT (C),A” instructions where reg C points to group of 4 lines and reg B points to 256 locations within the group of 4 lines. The following is allocation of video resources on the 4K RAM:

- regC=0x20 is first 4 lines where regB=0 is top left character of the display; and regB=63 is end of the first line; regB=64 is beginning of 2nd line; regB=128 is beginning of 3rd line; and regB=192 is beginning of 4th line.
- regC=0x21 is 2nd group of 4 lines, line 5 to line 8.
- so on,
- regC=0x2B is12th group of 4 lines, line 45 to line 48.
- regC=0x2C is font lookup table corresponding to character values 0x0 to 0x1F. Each font is 8×8 requiring 8 bytes to represent a character. RegB=0 corresponds to top 8 pixels of 8×8 font; the most significant bit is the leftmost pixel and the least significant bit is the rightmost pixel. RegB=1 to 7 correspond to next seven 8-pixel array of a font. RegB=8-0xF is font table for character value 0x1, RegB=0x10-0x17 is font table for character value 0x2, so on.
- regC=0x2D is font lookup table corresponding to character values 0x20 to 0x3F.
- regC=0x2E is font lookup table corresponding to character values 0x40 to 0x5F.
- regC=0x2F is font lookup table corresponding to character values 0x60 to 0x7F.
