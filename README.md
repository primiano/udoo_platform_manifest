udoo_platform_manifest
======================

This is a gitorious fork of Android for the UDOO Quad board (www.udoo.org).  
This is essentially a proper Android manifest, pointing to git subprojects, which allows to work on a fork of the reference UDOO Android image.  
This fork is based on [Android 4.3 Sources v2.0 (U-Boot, Kernel, File System)](http://download.udoo.org/files/Sources/UDOO_Android_4.3_Source_v2.0.tar.gz).  
There are currently two branches available:

 * **udoo-android-4.3-v2.0**: the original image untouched.
 * **master**: as above with my changes on top (SATA and S/PDIF support) .


**Sync the Android repo**

    repo init -u https://github.com/primiano/udoo_platform_manifest -b udoo-android-4.3-v2.0

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
