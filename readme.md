##Mastering Ubuntu Live CDs


Provide a customized installation / live Ubuntu CD image

Update can contain automatic system updates, security features, or just a pre-configured environment tailored for my use.

Advanced use include building specific cloud node configurations, for example a dedicated buildbot test node, or a specific application server.

##References




##Instructions


####Mastering a Life Ubuntu CD


    (dd if=/dev/cdrom of=./ubuntu-8.10.iso)
    mkdir -p ./isomount ./isonew/squashfs ./isonew/cd ./isonew/custom
    sudo mount -o loop ./ubuntu-8.10.iso ./isomount/
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
    sudo mksquashfs ./isonew/custom ./isonew/cd/casper/filesystem.squashfs
    sudo rm ./isonew/cd/md5sum.txt
    sudo -s
    cd ./isonew/cd
    * Edit DISKNAME parameter in ./isonew/README.diskdefines
    find . -type f -print0 | xargs -0 md5sum > md5sum.txt
    exit
    cd ./isonew/cd
    sudo mkisofs -r -V "Ubunto-Live-Private" -b isolinux/isolinux.bin -c isolinux/boot.cat -cache-inodes -J -l -no-emul-boot -boot-load-size 4 -boot-info-table -o ~/Ubunto-Live-PrivateSurf.iso .


*Note*
'Verify if the procedure works agains the newest Ubuntu releases, 12.10 at the time of writing'


