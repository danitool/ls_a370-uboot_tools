# U-Boot tools for Linkstation Armada370
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

## doimage:
*mvebuimg* may create a non working bootloader, in this case we can use *doimage* which is able
to recreate the original bootloader (exact copy). 

With *doimage* first we need to prepend 12 bytes to the *binary.0* header, for some reason they're
lost when *kwbimage* splits the bootloader.

In the Buffalo LS421DE, these bytes are: **020000005B00000068000000** (offsets 0x24-0x2f)  
In the Buffalo LS220DE, these bytes are: **020000005B00000000000000** (offsets 0x24-0x2f)

 * Command example for using doimage with u-boot 1.34:
```
echo -ne \\x02\\x00\\x00\\x00\\x5B\\x00\\x00\\x00\\x68\\x00\\x00\\x00 > binary.1
cat binary.0 >> binary.1

./doimage -T flash -D 0x600000 -E 0x6A0000 -G binary.1 payload u-boot.bin
```

 * For LS421DE 1.84, LS220DE 0.38:
```
./doimage -T flash -D 0x0 -E 0x0 -G binary.1 payload u-boot.bin
```
