VM Install

password br-edays


sudo apt-get update



sudo vmware-toolbox-cmd disk shrink /


sudo apt-get install open-vm-tools-desktop


sudo apt-get install git rpm build-essential pigz mc wireshark kdiff3-qt meld filezilla libncurses5-dev cmake make autoconf libtool automake pkg-config texinfo bison flex tree dos2unix putty gedit autogen tree doxygen genext2fs nfs-kernel-server bridge-utils uml-utilities qemu-system-arm qemu-kvm graphviz chrpath picocom moreutils



SUDO without a password
Reference: https://mintguide.org/other/355-how-to-make-sudo-without-a-password-on-linux-mint.html
sudo visudo
Find the line
# See sudoers(5) for more information on "#include" directives:
add below:
br-edays ALL=(ALL) NOPASSWD: ALL










git clone https://github.com/schenkmi/br-edays19-lab.git




NFS Boot


mkdir ~/rootfs

/etc/exports
/home/br-edays/rootfs *(rw,sync,no_subtree_check,no_root_squash,insecure)

sudo /etc/init.d/nfs-kernel-server restart

Test mit
sudo exportfs -av



    Filesystem images  --->     
│ │     [*] tar the root filesystem                                                │ │  
  │ │           Compression method (gzip)  --->                                      │ │  



cd ~/rootfs/
tar xzvf /home/br-edays/br-edays19-lab/buildroot-2019.05/output/images/rootfs.tar.gz




qemu-system-arm -M versatilepb -m 128M -kernel zImage -append "root=/dev/nfs nfsroot=10.0.2.2:/srv/nfs/_install rw ip=10.0.2.15::10.0.2.1:255.255.255.0 init=/sbin/init"




qemu-system-arm \
  -M versatilepb \
  -kernel output/images/zImage \
  -dtb output/images/versatile-pb.dtb \
  -append "root=/dev/nfs nfsroot=10.0.2.2:/home/br-edays/rootfs rw console=ttyAMA0,115200" \
  -serial stdio \
  -netdev user,id=ethernet.0,hostfwd=tcp::2222-:22,hostfwd=tcp::2223-:69 \
  -device rtl8139,netdev=ethernet.0 \
  -name Versatile_ARM_EXT2


qemu-system-x86_64 -enable-kvm -initrd /qemuChroot/boot/initrd.img-3.2.0-2-amd64 -kernel /qemuChroot/boot/vmlinuz-3.2.0-2-amd64 -append "root=/dev/nfs nfsroot=10.0.2.2:/qemuChroot" -netdev type=user,id=usernet -device netdev=usernet,driver=virtio-net


qemu-system-x86_64 -enable-kvm -initrd /qemuChroot/boot/initrd.img-3.2.0-2-amd64 -kernel /qemuChroot/boot/vmlinuz-3.2.0-2-amd64 -append "root=/dev/nfs nfsroot=10.0.2.2:/qemuChroot" -netdev type=user,id=usernet -device netdev=usernet,driver=virtio-net



qemu-system-arm \
  -M versatilepb \
  -kernel output/images/zImage \
  -dtb output/images/versatile-pb.dtb \
  -append "root=/dev/nfs nfsroot=10.0.2.3:/home/br-edays/rootfs rw init=/sbin/init console=ttyAMA0,115200" \
  -serial stdio \
  -netdev type=user,id=usernet -device netdev=usernet,driver=virtio-net \
  -name Versatile_ARM_EXT2



qemu-system-arm \
  -M versatilepb \
  -kernel output/images/zImage \
  -dtb output/images/versatile-pb.dtb \
  -append "root=/dev/nfs nfsroot=10.0.2.2:/home/br-edays/rootfs rw ip=10.0.2.15::10.0.2.1:255.255.255.0 init=/sbin/initconsole=ttyAMA0,115200" \
  -serial stdio \
  -netdev type=user,id=usernet -device netdev=usernet,driver=virtio-net \
  -name Versatile_ARM_EXT2



qemu-system-arm -M versatilepb -m 128M -kernel zImage -append "root=/dev/nfs nfsroot=10.0.2.2:/srv/nfs/_install rw ip=10.0.2.15::10.0.2.1:255.255.255.0 init=/sbin/init"


qemu-system-arm \
  -M versatilepb \
  -kernel output/images/zImage \
  -dtb output/images/versatile-pb.dtb \
  -append "root=/dev/nfs nfsroot=10.0.2.2:/home/br-edays/rootfs rw ip=10.0.2.15::10.0.2.1:255.255.255.0 init=/sbin/init console=ttyAMA0,115200" \
  -serial stdio \
  -name Versatile_ARM_EXT2

qemu-system-arm \
  -M versatilepb \
  -kernel output/images/zImage \
  -dtb output/images/versatile-pb.dtb \
  -append "root=/dev/nfs nfsroot=10.0.2.2:/home/br-edays/rootfs rw ip=10.0.2.15::10.0.2.1:255.255.255.0 init=/sbin/init console=ttyAMA0,115200 nfsdebug" \
  -serial stdio \
  -name Versatile_ARM_EXT2

qemu-system-arm \
  -M versatilepb \
  -kernel output/images/zImage \
  -dtb output/images/versatile-pb.dtb \
  -append "root=/dev/nfs nfsroot=10.0.2.3:/home/br-edays/rootfs,udp rw ip=10.0.2.15::10.0.2.1:255.255.255.0 init=/sbin/init console=ttyAMA0,115200 nfsdebug" \
  -serial stdio \
  -name Versatile_ARM_EXT2



qemu-system-arm \
  -M versatilepb \
  -kernel output/images/zImage \
  -dtb output/images/versatile-pb.dtb \
  -append "root=/dev/nfs nfsroot=10.0.2.3:/home/br-edays/rootfs rw ip=10.0.2.15::10.0.2.3:255.255.255.0 init=/sbin/init console=ttyAMA0,115200 nfsdebug" \
  -serial stdio \
  -netdev user,id=ethernet.0,hostfwd=tcp::2222-:22,hostfwd=tcp::2223-:69 \
  -device rtl8139,netdev=ethernet.0 \
  -name Versatile_ARM_EXT2



sudo brctl addbr br0
sudo ip addr flush dev ens33
sudo brctl addif br0 ens33
sudo tunctl -t tap0 -u `whoami`
sudo brctl addif br0 tap0
sudo ifconfig ens33 up
sudo ifconfig tap0 up
sudo ifconfig br0 up
sudo dhclient -v br0





NFS qemu on MINT19.1 use ,vers=3 does the trick :-)

qemu-system-arm \
  -M versatilepb \
  -kernel output/images/zImage \
  -dtb output/images/versatile-pb.dtb \
  -append "root=/dev/nfs nfsroot=10.0.2.2:/home/br-edays/rootfs,vers=3 rw ip=dhcp init=/sbin/init console=ttyAMA0,115200" \
  -serial stdio \
  -netdev user,id=ethernet.0,hostfwd=tcp::2222-:22,hostfwd=tcp::2223-:69 \
  -device rtl8139,netdev=ethernet.0 \
  -name Versatile_ARM_EXT2



tail -f /var/log/syslog



Picocom
sudo picocom -b 115200 /dev/ttyUSB0











Getting Buildroot
wget https://www.buildroot.org/downloads/buildroot-2019.05.tar.bz2

Unpack with
tar xjvf Downloads/buildroot-2019.05.tar.bz2



git commit -m "2019.07.03 initial import"
git push -u origin master

git tag -a init -m "2019.07.03 initial import"
git push origin --tags



qemu-system-arm \
  -M versatilepb \
  -kernel output/images/zImage \
  -dtb output/images/versatile-pb.dtb \
  -drive file=output/images/rootfs.ext4,if=scsi,format=raw \
  -append "root=/dev/sda console=ttyAMA0,115200" \
  -serial stdio \
  -netdev user,id=ethernet.0,hostfwd=tcp::2222-:22,hostfwd=tcp::2223-:69 \
  -device rtl8139,netdev=ethernet.0 \
  -name Versatile_ARM_EXT2


Legacy redirect
qemu-system-arm \
  -M versatilepb \
  -kernel output/images/zImage \
  -dtb output/images/versatile-pb.dtb \
  -drive file=output/images/rootfs.ext4,if=scsi,format=raw \
  -append "root=/dev/sda console=ttyAMA0,115200" \
  -serial stdio \
  -net nic,model=rtl8139 -net user \
  -redir tcp:2222::22 \
  -redir tcp:2223::69 \
  -name Versatile_ARM_EXT2
















LAB1

cd buildroot-2019.05/
make qemu_arm_versatile_defconfig

make linux-menuconfig




[*] Networking support  ---> 
    Networking options  --->
      [*]   IP: kernel level autoconfiguration  
      [*]     IP: DHCP support
      [*]     IP: BOOTP support
      [*]     IP: RARP support 


File systems  --->
[*] Network File Systems  ---> 
--- Network File Systems
<*>   NFS client support 
<*>     NFS client support for NFS version 2 (NEW)    
<*>     NFS client support for NFS version 3 (NEW) 
[ ]       NFS client support for the NFSv3 ACL protocol extension (NEW) 
<*>     NFS client support for NFS version 4                      
[ ]     Provide swap over NFS support (NEW)   
[ ]   NFS client support for NFSv4.1 (NEW) 
[*]   Root file system on NFS 


make linux-update-defconfig




qemu-system-arm \
  -M versatilepb \
  -kernel output/images/zImage \
  -dtb output/images/versatile-pb.dtb \
  -drive file=output/images/rootfs.ext2,if=scsi,format=raw \
  -append "root=/dev/sda console=ttyAMA0,115200" \
  -serial stdio \
  -net nic,model=rtl8139 -net user \
  -redir tcp:2222::22 \
  -name Versatile_ARM_EXT2


git tag -a lab1_start -m "2019.07.03 ready for LAB1"
git push origin --tags


Aufgabe
- Definieren des Rootpassworted noser

    System configuration  --->
(noser) Root password       

- hinzufügen des Packetes Dropbear

 Target packages  --->
    Networking applications  --->        
      [*] dropbear
      [*]   client programs (NEW)
      [ ]   disable reverse DNS lookups (NEW)
      [*]   optimize for size (NEW)
      [ ]   log dropbear access to wtmp (NEW)
      [ ]   log dropbear access to lastlog (NEW)
      [ ]   enable legacy crypto (NEW) 


- ändern des root filesystems auf ext4

    Filesystem images  ---> 
[*] ext2/3/4 root filesystem
      ext2/3/4 variant (ext4)  --->


make menuconfig


make

qemu-system-arm \
  -M versatilepb \
  -kernel output/images/zImage \
  -dtb output/images/versatile-pb.dtb \
  -drive file=output/images/rootfs.ext4,if=scsi,format=raw \
  -append "root=/dev/sda console=ttyAMA0,115200" \
  -serial stdio \
  -net nic,model=rtl8139 -net user \
  -redir tcp:2222::22 \
  -name Versatile_ARM_EXT2




 ssh -p2222 root@127.0.0.1




Absteichern neue default config 

make update-defconfig


git commit -m "end of LAB1"

git tag -a lab1_end -m "2019.07.03 end of LAB1"
git push origin --tags



git tag -a lab2_start -m "2019.07.04 ready for LAB2"
git push origin --tags

git tag -a lab2_end -m "2019.07.04 end of LAB2"
git push origin --tags


git tag -a lab3_start -m "2019.07.04 ready for LAB3"
git push origin --tags


package/hello-noser

package/hello-noser/hello-noser.cpp
#include <iostream>

int main()
{
    std::cout << "Hello Noser!\n";
    return 0;
}

package/hello-noser/CMakeLists.txt

cmake_minimum_required(VERSION 2.4)

project(hello_noser)

add_executable(hello-noser.cpp)


package/Config.in:

menu "Noser E-Days"
    source "package/tftpd/Config.in"
	source "package/hello-noser/Config.in"
endmenu


package/hello-noser/Config.in:

config BR2_PACKAGE_HELLO_NOSER
    bool "hello_noser"
    help
        Hello Noser package.

        http://www.noser.com



package/hello/hello-noser.mk:


################################################################################
#
# hello
#
################################################################################

HELLO_VERSION = 1.0
HELLO_SITE = ./package/hello/src
HELLO_SITE_METHOD = local

define HELLO_BUILD_CMDS
    $(MAKE) CC="$(TARGET_CC)" LD="$(TARGET_LD)" -C $(@D)
endef

define HELLO_INSTALL_TARGET_CMDS
    $(INSTALL) -D -m 0755 $(@D)/hello $(TARGET_DIR)/usr/bin
endef

$(eval $(generic-package))





make hello-noser-dirclean && make hello-noser

qemu-system-arm \
  -M versatilepb \
  -kernel output/images/zImage \
  -dtb output/images/versatile-pb.dtb \
  -drive file=output/images/rootfs.ext4,if=scsi,format=raw \
  -append "root=/dev/sda console=ttyAMA0,115200" \
  -serial stdio \
  -net nic,model=rtl8139 -net user \
  -redir tcp:2222::22 \
  -redir udp:2223::69 \
  -name Versatile_ARM_EXT2

# hello-noser 
Hello Noser :-)
# 

hello-noser.mk


git tag -a lab4_start -m "2019.07.04 ready for LAB4"
git push origin --tags


git tag -a -f lab4_start d30f789
git push --tags --force

make BR2_EXTERNAL=../noser-external menuconfig

make BR2_EXTERNAL=../noser-external hello-noser-dirclean

make BR2_EXTERNAL=/work/br-edays19-lab/noser-external hello-noser



https://gist.github.com/sbourdelin/9668e8d3071cdbc4f81c


make BR2_EXTERNAL=../noser-external noser_defconfig
make BR2_EXTERNAL=../noser-external menuconfig


├── board
│   └── noser
│       └── qemu_arm_versatile
│           ├── patches
│           └── rootfs_overlay
│               └── etc
├── Config.in
├── configs
│   └── noser_defconfig
├── external.desc
├── external.mk
├── package
│   ├── hello-noser
│   │   ├── Config.in
│   │   ├── hello-noser.mk
│   │   └── src
│   │       ├── CMakeLists.txt
│   │       └── hello-noser.c
│   └── hello-noser2
│       ├── Config.in
│       ├── hello-noser2.mk
│       └── src
│           ├── hello-noser2.c
│           └── Makefile
└── patches
    └── libunwind
        └── 0001-libunwind-fPIC.patch

14 directories, 13 files

make BR2_EXTERNAL=../noser-external update-defconfig



git tag -a lab4_end -m "2019.07.05 end of LAB4"
git push origin --tags


git tag -a -f lab4_end 3df3257
git push --tags --force



git tag -a lab5_start -m "2019.07.05 ready for LAB5"
git push origin --tags


git commit -m "end of LAB5" .
git tag -a lab5_end -m "2019.07.09 end of LAB5"
git push origin --tags



git tag -a lab6_start -m "2019.07.16 ready for LAB6"
git push origin --tags


git commit -m "end of LAB6" .
git tag -a lab6_end -m "2019.07.16  end of LAB6"
git push && git push --tags


http://blog.fourthbit.com/2013/06/18/creating-an-open-source-program-in-c-with-autotools-part-1-of-2

~/br-edays19-lab/buildroot-2019.05

~/br-edays19-lab/buildroot-2019.05/output/

export BR_HOME=~/br-edays19-lab/buildroot-2019.05
export BR_SYSROOT=$(BR_HOME)/host/usr/arm-buildroot-linux-uclibcgnueabi/sysroot

export BR_PKG_CONFIG="$(BR_HOME)/host/usr/bin/pkg-config"

export BR_PKG_CONFIG_LIBDIR=$(BR_SYSROOT)/usr/lib
export BR_PKG_CONFIG_PATH="$(SYSROOT)/usr/lib/pkgconfig:$(SYSROOT)/usr/share/pkgconfig:$(SYSROOT)/lib/pkgconfig"






PKG_CONFIG="$(BR_PKG_CONFIG)" PKG_CONFIG_SYSROOT_DIR="$(BR_SYSROOT)" PKG_CONFIG_LIBDIR="$(BR_PKG_CONFIG_LIBDIR)" PKG_CONFIG_PATH="$(BR_PKG_CONFIG_PATH)"


OHUPNPPLAYER_DEF=--host=$(CONFIGURE_HOSTTARGET) --build=i686-linux --target=$(CONFIGURE_HOSTTARGET) \
				 --with-crasher --with-ffmpegproxy --with-platinum 									\
				 --prefix=$(OHUPNPPLAYER_DIR)/install/usr/local



configure --host=arm-linux --build=i686-linux --target=arm-linux --prefix=



or 


configure --host=arm-buildroot-linux-uclibcgnueabi-

.bashrc

.17


if [ -f ~/.bash_br ]; then
    . ~/.bash_br
fi


~/.bash_br

# M.Schenk 2019.07.17
# included by .bashrc

function set-title(){
 if [[ -z "$ORIG" ]]; then
    ORIG=$PS1
  fi
  TITLE="\[\e]2;$*\a\]"
  PS1=${ORIG}${TITLE}
}

export BR_HOME=~/br-edays19-lab/buildroot-2019.05
export BR_OUTPUT=$BR_HOME/output
export BR_HOST_BIN=$BR_OUTPUT/host/bin
export BR_SYSROOT=$BR_OUTPUT/host/usr/arm-buildroot-linux-uclibcgnueabi/sysroot
export BR_PKG_CONFIG=$BR_OUTPUT/host/usr/bin/pkg-config
export BR_PKG_CONFIG_LIBDIR=$BR_SYSROOT/usr/lib
export BR_PKG_CONFIG_PATH=$BR_SYSROOT/usr/lib/pkgconfig:$BR_SYSROOT/usr/share/pkgconfig:$BR_SYSROOT/lib/pkgconfig
alias cdbr='cd $BR_HOME'

./share/libtool/configure --host=arm-linux --build=i686-linux --target=arm-linux


PATH=$BR_HOST_BIN:$PATH


make distclean
PATH=$BR_HOST_BIN:$PATH ./configure --host=arm-linux --build=i686-linux --target=arm-linux
PATH=$BR_HOST_BIN:$PATH make
PATH=$BR_HOST_BIN:$PATH make install-strip DESTDIR=`pwd`/install


make distclean
PATH=$BR_HOST_BIN:$PATH ./configure --host=arm-buildroot-linux-uclibcgnueabi --build=i686-linux --target=arm-buildroot-linux-uclibcgnueabi
PATH=$BR_HOST_BIN:$PATH make
PATH=$BR_HOST_BIN:$PATH make install-strip DESTDIR=`pwd`/install

make maintainer-clean

	new file:   edays-automake/.gitignore
	new file:   edays-automake/AUTHORS
	new file:   edays-automake/COPYING
	new file:   edays-automake/ChangeLog
	new file:   edays-automake/INSTALL
	new file:   edays-automake/Makefile.am
	new file:   edays-automake/NEWS
	new file:   edays-automake/README
	new file:   edays-automake/configure.ac
	new file:   edays-automake/src/Makefile.am
	new file:   edays-automake/src/main.c




git tag -a lab7_start -m "2019.07.18 ready for LAB7"
git push origin --tags

see http://blog.fourthbit.com/2013/06/18/creating-an-open-source-program-in-c-with-autotools-part-1-of-2

autoreconf -iv
git clean -f -d -x .

autoreconf -iv

schenk@schenk-linux:/work/br-edays19-lab/edays-automake$ git status --ignored -sb .
## master...origin/master
!! Makefile.in
!! aclocal.m4
!! autom4te.cache/
!! compile
!! config.h.in
!! configure
!! depcomp
!! install-sh
!! missing
!! src/Makefile.in



git status --ignored -sb .




PATH=$BR_HOST_BIN:$PATH PKG_CONFIG=$BR_PKG_CONFIG PKG_CONFIG_SYSROOT_DIR=$BR_SYSROOT PKG_CONFIG_LIBDIR=$BR_PKG_CONFIG_LIBDIR PKG_CONFIG_PATH=${BR_PKG_CONFIG_PATH} ./configure --host=arm-linux --build=i686-linux --target=arm-linux


./build/libcurl-7.65.0/libcurl.pc
#***************************************************************************
#                                  _   _ ____  _
#  Project                     ___| | | |  _ \| |
#                             / __| | | | |_) | |
#                            | (__| |_| |  _ <| |___
#                             \___|\___/|_| \_\_____|
#
# Copyright (C) 1998 - 2012, Daniel Stenberg, <daniel@haxx.se>, et al.
#
# This software is licensed as described in the file COPYING, which
# you should have received as part of this distribution. The terms
# are also available at https://curl.haxx.se/docs/copyright.html.
#
# You may opt to use, copy, modify, merge, publish, distribute and/or sell
# copies of the Software, and permit persons to whom the Software is
# furnished to do so, under the terms of the COPYING file.
#
# This software is distributed on an "AS IS" basis, WITHOUT WARRANTY OF ANY
# KIND, either express or implied.
#
###########################################################################

# This should most probably benefit from getting a "Requires:" field added
# dynamically by configure.
#
prefix=/usr
exec_prefix=/usr
libdir=${exec_prefix}/lib
includedir=${prefix}/include
supported_protocols="DICT FILE FTP GOPHER HTTP IMAP POP3 RTSP SMTP TELNET TFTP"
supported_features="IPv6 UnixSockets AsynchDNS"

Name: libcurl
URL: https://curl.haxx.se/
Description: Library to transfer files with ftp, http, etc.
Version: 7.65.0
Libs: -L${libdir} -lcurl
Libs.private: 
Cflags: -I${includedir} 




PATH=$BR_HOST_BIN:$PATH PKG_CONFIG=$BR_PKG_CONFIG PKG_CONFIG_SYSROOT_DIR=$BR_SYSROOT PKG_CONFIG_LIBDIR=$BR_PKG_CONFIG_LIBDIR PKG_CONFIG_PATH=$BR_PKG_CONFIG_PATH ./configure --host=arm-linux --build=i686-linux --target=arm-linux



schenk@schenk-linux:/work/br-edays19-lab/edays-automake$ PKG_CONFIG_PATH=/work/br-edays19-lab/buildroot-2019.05/output/host/usr/arm-buildroot-linux-uclibcgnueabi/sysroot/usr/lib/pkgconfig /work/br-edays19-lab/buildroot-2019.05/output/host/usr/bin/pkg-config libcurl -cflags
-I/work/br-edays19-lab/buildroot-2019.05/output/host/usr/bin/../arm-buildroot-linux-uclibcgnueabi/sysroot/usr/include 





