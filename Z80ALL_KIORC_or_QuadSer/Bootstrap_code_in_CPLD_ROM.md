# ROMBOOTCF, A Small Bootstrap ROM in CPLD
This is a small (26 bytes) bootstrap program in Z80all's CPLD that copies 256 bytes of data from CF's master boot record to memory at 0xB000 and jump to 0xB000

```
CFdata       equ 010h        ;CF data register
CFstat       equ 017h           ;CF status/command reg

    ld hl,0b000h    ;bootstrap code starts from 0xb000
readbsy:
    in a,(CFstat)    ; read CF status
    rla        ; check busy bit
    jr c,readbsy
    ld c,CFdata    ; reg C points to CF data reg
    ld a,20h        ; read sector command
    out (CFstat),a    ; issue the read sector command
chkdrq:
    in a,(CFstat)    ; check data request bit set before read CF data
    and 8        ; bit 3 is DRQ, wait for it to set
    jr z,chkdrq
    inir        ;z80 read 256 bytes
    jp 0b000h
```
