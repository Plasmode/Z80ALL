# Update monitor for Z80all with KIORC
*This was an email to an user of Z80all with KIORC explaining how to update the monitor software.*

I finally figured out why KIO is intermittent. It is because of an unintialized register that affects the auto detection of KIO. Once the register is properly initialized, KIO is detected consistently. If your KIO is not talking to the terminal, enter this on your PS2 keyboard
gb4a5 (go to address 0xb4a5) and you should see the sign on message:
ZALLQ monitor v0.9 2/20/23
KIO detected

I also fixed the bugs with some PS2 keys. I also fixed a bug associated with CapLock. Attached are the revised Z80all monitor, rev 0.91. Included with the updated monitor are two files needed to copy the revised monitor into CF disk. Assuming you have working serial communication using KIO, here are the steps necessary to update the CF disk with new monitor. (I apologize for the rather complicated process; for long time I was the only user so I didn't mind all these steps)
1. send zakserld.hex to Z80all
2. enter “gb000” to execute zakserld. This is a hex file loader for loading new monitor
3. send updated monitor, zallkqmon.hex. the monitor will boot and display the sign-on message
…v0.91 3/28/23
KIO detected
The new monitor now resides in RAM, but not in CF disk so reset or power cycle will restore the original monitor. To copy the new monitor into CF disk:
4. send cfbootloaderq.hex
5. gb300 (this will copy new monitor into CF disk).
