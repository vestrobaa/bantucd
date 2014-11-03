##Mastering Ubuntu Live CDs

Provide a customized live installation Ubuntu CD image. Mastering a live CD is a way of precofiguring the ISO with software, settings and updates.


####Typical usage

- Automatic system updates
- Security features
- Pre-configured environment tailored for my use
- Cloud node configurations, for example a dedicated buildbot test node, or a specific application server.


###References

- [Ubuntu Live CD Custimzation][LiveCDCustomization]
- Paranoid Penguin, Customizing Live CDs Parts [1], [2] and [3]
- [Squashfs][squashfs]


###Instructions

- Grab a base Ubuntu or Debian based Linux distribution
- Follow the mastering process below
- Change the process to swap out software on the ISO
- Use the newly mastered ISO as a live boot or an installation source


###Mastering process

    (dd if=/dev/cdrom of=./ubuntu-14.04-desktop-i386.iso)
    mkdir -p ./isomount ./isonew/squashfs ./isonew/cd ./isonew/custom
    sudo mount -o loop ./ubuntu-14.04-desktop-i386.iso ./isomount/
    rsync --exclude=/casper/filesystem.squashfs -a ./isomount/ ./isonew/cd
    sudo modprobe squashfs
    sudo mount -t squashfs -o loop ./isomount/casper/filesystem.squashfs ./isonew/squashfs/
    sudo rsync -a ./isonew/squashfs/ ./isonew/custom
    sudo cp /etc/resolv.conf /etc/hosts ./isonew/custom/etc
    sudo cp /etc/apt/sources.list ./isonew/custom/etc/apt/
    sudo chroot ./isonew/custom
    mount -t proc none /proc/
    mount -t sysfs none /sys/
    export HOME=/root
    apt-get remove --purge `dpkg-query -W --showformat='${Package}\n' | grep openoffice`
    apt-get remove --purge `dpkg-query -W --showformat='${Package}\n' | grep libreoffice`
    apt-get remove --purge `dpkg-query -W --showformat='${Package}\n' | grep gimp`
    apt-get update
    apt-get install tor privoxy
    apt-get dist-upgrade
    apt-get clean
    rm -rf /tmp/*
    umount /proc/
    umount /sys/
    exit
    chmod +w ./isonew/cd/casper/filesystem.manifest
    sudo chroot ./isonew/custom dpkg-query -W --showformat='${Package} ${Version}\n' > ./isonew/cd/casper/filesystem.manifest
    sudo cp ./isonew/cd/casper/filesystem.manifest ./isonew/cd/casper/filesystem.manifest-desktop
    (sudo apt-get install squashfs-tools)
    sudo mksquashfs ./isonew/custom ./isonew/cd/casper/filesystem.squashfs
    sudo rm ./isonew/cd/md5sum.txt
    sudo -s
    cd ./isonew/cd
    * Edit DISKNAME parameter in ./README.diskdefines
    find . -type f -print0 | xargs -0 md5sum > md5sum.txt
    exit
    cd ./isonew/cd
    sudo mkisofs -r -V "Ubuntu-14-04-July-i386" -b isolinux/isolinux.bin -c isolinux/boot.cat -cache-inodes -J -l -no-emul-boot -boot-load-size 4 -boot-info-table -o ~/Ubuntu_14_04_July_Custom_Update_i386.iso .


[LiveCDCustomization]:https://help.ubuntu.com/community/LiveCDCustomization "Live CD Customization"
[1]:http://www.linuxjournal.com/magazine/paranoid-penguin-customizing-linux-live-cds-part-i "Part 1"
[2]:http://www.linuxjournal.com/magazine/paranoid-penguin-customizing-linux-live-cds-part-ii "Part 2"
[3]:http://www.linuxjournal.com/magazine/paranoid-penguin-customizing-linux-live-cds-part-iii "Part 3"
[squashfs]:http://www.tldp.org/HOWTO/SquashFS-HOWTO/creatingandusing.html "SquashFS"

