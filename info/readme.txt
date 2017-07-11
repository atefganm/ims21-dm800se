Run dm800se from USB stick:

BUILD IMAGE:
============
I) patch openpli-bootlogo.bb file - This step I) is not needed, if you have backup of this 3 files (content_fat32.zip):
=======================================================================================================================
autoexec_dm800se.bat
bootlogo-dm800se.elf.gz
bootlogo-dm800se.jpg  ... for splash screen you can use own jpg 1280x720

patch:
@@ -24,8 +24,13 @@
 	file://switchoff.mvi \
 	file://bootlogo.sh \
 	"
 
+BINARY_VERSION = "1.3"
+SRC_URI += "${@bb.utils.contains("MACHINE_FEATURES", "dreambox", "http://dreamboxupdate.com/download/opendreambox/2.0.0/dreambox-bootlogo/dreambox-bootlogo_${BINARY_VERSION}_${MACHINE_ARCH}.tar.bz2;name=${MACHINE_ARCH}" , "", d)}"
+SRC_URI[dm800se.md5sum] = "3413a894a3d77e02cae34b96d302817d"
+SRC_URI[dm800se.sha256sum] = "8a283442c231e82ee1a2093e53dc5bf52c478e12d22c79af7e7026b52711fc9c"
+
 MVI = "${SWITCHOFFMVI} bootlogo.mvi"
 MVISYMLINKS = "bootlogo_wait backdrop"
 
 do_install() {
@@ -34,8 +39,9 @@
 	for i in ${MVI}; do
 		install -m 0755 ${S}/$i ${D}/usr/share/
 		ln -sf /usr/share/$i ${D}/boot/$i
 	done
+	${@bb.utils.contains("MACHINE_FEATURES", "dreambox", "install -m 0755 ${S}/dreambox-bootlogo_${BINARY_VERSION}_${MACHINE_ARCH}/bootlogo-${MACHINE_ARCH}.elf.gz ${D}/boot/; install -m 0755 ${S}/dreambox-bootlogo_${BINARY_VERSION}_${MACHINE_ARCH}/bootlogo-${MACHINE_ARCH}.jpg ${D}/boot/", "", d)}
 	for i in ${MVISYMLINKS}; do
 		ln -sf /boot/bootlogo.mvi ${D}/boot/$i.mvi
 		ln -sf /usr/share/bootlogo.mvi ${D}/usr/share/$i.mvi;
 	done


II) there in .../dm800se/conf/machine/dm800se.conf you can remove unused features from image - I am using this only:

OPENPLI_FEATURES = "fan usbconsole"
MACHINE_FEATURES += "hdmicec"

uncomment line, if your python is free all wins files (whl, exe):

IMAGE_INSTALL_remove = "samba-base"

Builded image then will be successfully created ... will be less than 64MB

III) build image



PREPARE USB STICK - create partitions ... in gparted:
========================================================

I) CREATE PARTITIONS
--------------------
1. create cca 10MB Win FAT32 table (depends on usb size, f.eg. 16GB flash will be smalles as 33MB)
2. make it as boot
3. create cca 500MB Linux EXT4 table
4. create Linux EXT4 table (rest of size) ... for using as USB, if you want - optionaly

all must be formated


II) in some mips box or on linux machine:
-----------------------------------------
1. If you have backup of this 3 files, copy files to fat32:

autoexec_dm800se.bat
bootlogo-dm800se.elf.gz
bootlogo-dm800se.jpg

(or you can get in later in step 2 from /boot directory)

Notes for FAT32 content:

- content of autoexec_dm800se.bat should be:
/boot/bootlogo-dm800se.elf.gz filename=/boot/bootlogo-dm800se.jpg
/boot/vmlinux-3.2-dm800se.gz root=/dev/sdb2 rootfstype=ext4 rootdelay=10 rw console=null

- bootlogo-dm800se.jpg can by any 1280*720 jpg picture

- /dev/sdb2 ... depends, how it will be mounted (sda is internal hdd, if is installed, sdb is usb on dm800se, if is installed hdd)

2. unpack dm800se image into 2nd partition (=first linux partition) - use nfidump for mipsel or for linux in utility.zip

./nfidump ./openpli-enigma2-homebuild-dm800se.nfi /dev/sdX2/

- sdX2 means sda2 or sdb2 ... depends how is mounted usb stick

3. add vmlinux-3.2-dm800se.gz from image's boot to fat32:
cp /dev/sdX2/boot/vmlinux-3.2-dm800se.gz /dev/sdX1/

- sdX1 means sda1 or sdb1 ... depends how is mounted usb stick
umount all usb

4. STOP mode (without pluged boot USB) and change Boot in BIOS via telnet/putty (can see screenshot):

Primary Boot Source:	usb 	[/autoexec_dm800se.bat  ]
Secondary Boot Source:	flash	[			]

Enable USB support	[ENABLED]

Save, reboot ...


After install box use in telnet command blkid to know UUID and edit /etc/fstab fro fat32 (sdX1) and 2nd linux partition (sdX3)
UID=XXXX-XXXX /media/.auto auto defaults 0 0
UUID=58baccdd-XXXX-XXXX-XXXX-XXXXXXXXXXXX /media/usb auto defaults 0 0 



