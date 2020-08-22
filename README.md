# U-Boot tools for Linstation Armada370
 * Tools for splitting an joining the U-boot header on Armada 370 based boards.  
 * Tool for for recovering via UART.  

Tested only on Linkstation devices based on Marvell Armada 370.

## Create uboot.uart (used for recovery):
```
./kwbimage -x -i u-boot.buffalo-orig -o tmp/  
./mvebuimg -v 1 create -b tmp/binary.0 -o uboot/uboot.uart uart tmp/payload
```
`rm tmp/*`  

## Uboot recovery instructions:
1. Connect the serial port to the Linkstation LS421DE

2. At the PC execute:  
   `./kwboot -b uboot/uboot.uart -t -B 115200 /dev/ttyUSB0`  

3. Power on the Linkstation, wait for it to load.

4. At the Uboot console execute:  
   `bubt uboot-spi.bak`

## Create uboot with a RAM initialization header from another uboot:
```
./kwbimage -x -i uboot/u-boot.buffalo-orig -o tmp/
mv tmp/payload tmp/payload.orig
```  
`./kwbimage -x -i uboot/u-boot.buffalo-1.34_voodoo -o tmp/`  
`./mvebuimg -v 1 create -b tmp/binary.0 -o uboot/uboot-voodoo.spi spi tmp/payload.orig`  
`rm tmp/*`
