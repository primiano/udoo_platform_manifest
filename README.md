UDOO Android manifest
======================

This is a repo-gitorious fork of Android for the UDOO Quad board (www.udoo.org).  
This is essentially a proper Android manifest, pointing to git subprojects, which allows to work on a fork of the reference UDOO Android image.  
This fork is based on [Android 4.3 Sources v2.0 (U-Boot, Kernel, File System)](http://download.udoo.org/files/Sources/UDOO_Android_4.3_Source_v2.0.tar.gz).  
The following branches are available:

 * **udoo-android-4.3-v2.0**: the original image (some irrelevant projects stripped, see notes below).
 * **udoo-android-4.3-spdif**: as above + changes for S/PDIF output.
 * **udoo-android-4.3-v2.0-with-sata**: as above + SATA suport (needs a partition on the HDD)
 * **master**: Bleeding edge / experimental.

If you are interested or just curious about how to create / maintain you own Android fork on GitHub take a look to this article here [http://www.primianotucci.com/blog/fork-android-on-github](http://www.primianotucci.com/blog/fork-android-on-github)


Building
========
General rules for building Android.

**Prepare the system**

    sudo apt-get install git gnupg flex bison gperf build-essential zip curl \
      libc6-dev libncurses-dev x11proto-core-dev libreadline-dev g++-multilib \
      mingw32 tofrodos python-markdown libxml2-utils xsltproc zlib1g-dev \
      libc6-i386 lib32stdc++6 lib32gcc1 lib32ncurses5 lib32z1 ia32-libs \
      file psmisc uuid-dev liblzo2-dev

    git config --global user.name "My Name"
    git config --global user.email "my@email.com"  # These are required to repo sync

**Install Oracle Java 6 SDK**  
Oracle's Java 6 SDK is required to build Android. You need this version (it doesn't have to be the default, though). 
Don't try to install other versions or use OpenJDK. It will just not work (at least, not for Android 4.3).

 Download [jdk-6u45-linux-x64.bin from the Oracle website](http://www.oracle.com/technetwork/java/javase/downloads/java-archive-downloads-javase6-419409.html#jdk-6u45-oth-JPR)

    chmod +x jdk-6u45-linux-x64.bin
    ./jdk-6u45-linux-x64.bin
    sudo chown root. -R jdk1.6.0_45
    sudo mv jdk1.6.0_45 /usr/lib/jvm/java-6-sun
    export JAVA_HOME=/usr/lib/jvm/java-6-sun
    export ANDROID_JAVA_HOME=$JAVA_HOME

**Download repo**

    curl https://storage.googleapis.com/git-repo-downloads/repo > /tmp/repo
    sudo cp /tmp/repo /usr/local/bin/repo
    sudo chmod 755 /usr/local/bin/repo

**Sync the Android repo**

    mkdir udoo && cd udoo
    repo init -u https://github.com/primiano/udoo_platform_manifest -b udoo-android-4.3-v2.0
    repo sync -c -j4  # Can do -j20 if your internet connection is very fast (50+ MB/s)

**Set up the build environment**

    export ARCH=arm
    export CROSS_COMPILE=$PWD/prebuilts/gcc/linux-x86/arm/arm-eabi-4.6/bin/arm-eabi-
    export PATH=$PWD/bootable/bootloader/uboot-imx/tools:$PATH
    source build/envsetup.sh
    lunch udoo-eng

**Build**

    # Build the bootloader first
    pushd .
    cd bootable/bootloader/uboot-imx
    popd
    # Then the Android image
    make -j8 droid    

**Flash**

    # Find out the device for the sdcard with lsblk, e.g., /dev/sdc
    sudo OUT=$OUT ./make_sd.sh /dev/sdc

**SATA HDD partitions layout**  
The master branch is configured to mount /data from a SATA HDD rather than the sdcard.
The expected partitions layout is defined in  [device/fsl/udoo/fstab.freescale](https://github.com/primiano/udoo_device_fsl/blob/master/udoo/fstab.freescale) as follows:

    /dev/block/sda1  /data   ext4  # This partition should take all the HDD space

You can prepare your HDD, on a Linux machine, as follows:

    sudo cfdisk /dev/sdX  # where sdX is the name of the device that you will attach to the board
    # Create only one Linux partition which covers all the device
    sudo mkfs.ext4 /dev/sdX1
    

**Additional notes**  
In order to reduce sync time and size, the following subprojects have been stripped out w.r.t. the original image:

 * /cts
 * /device/asus
 * /device/generic/goldfish
 * /device/generic/mini-emulator-armv7-a-neon
 * /device/generic/mini-emulator-mips
 * /device/generic/mini-emulator-x86
 * /device/generic/mips
 * /device/generic/x86
 * /device/google
 * /device/lge
 * /device/sample
 * /device/samsung
 * /device/samsung_slsi
 * /device/ti
 * /docs
 * /external/eclipse-basebuilder
 * /external/eclipse-windowbuilder
 * /packages/apps/ES_File_Explorer *(Redistribution grants are not clear to me for this app. No license is attached)*

If you want to know more about how to maintain your custom Android fork, you can read more here:  
[www.primianotucci.com/blog/fork-android-on-github](//www.primianotucci.com/blog/fork-android-on-github)
