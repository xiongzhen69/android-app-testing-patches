9/1/2011
Wind River

These patches have been tested with Gingerbread 2.3.5
and 2.3.4.  They will very likely work for earlier versions
as well.

If some of the patches do not apply, all is not lost --
they are usually really easy to apply by hand.


How to Apply these Patches
==========================
The patches are applied as follows, assuming
~/android/ginger-build is the directory where you did the 
original "repo init" for gingerbread and 
~/changes/ginger-patch is where you unpacked these patch files:

frameworks
----------
$ cd ~/android/ginger-build
$ cd frameworks/base
$ git apply --check ~/changes/ginger-patch/frameworks
$ git apply ~/changes/ginger-patch/frameworks

gallery
-------
$ cd ~/android/ginger-build
$ cd packages/apps/Gallery
$ git apply --check ~/changes/ginger-patch/gallery
$ git apply ~/changes/ginger-patch/gallery

gallery3d
---------
$ cd ~/android/ginger-build
$ cd packages/apps/Gallery3D
$ git apply --check ~/changes/ginger-patch/gallery3d
$ git apply ~/changes/ginger-patch/gallery3d

camera
------
$ cd ~/android/ginger-build
$ cd packages/apps/Camera
$ git apply --check ~/changes/ginger-patch/camera
$ git apply ~/changes/ginger-patch/camera

launcher2
---------
$ cd ~/android/ginger-build
$ cd packages/apps/Launcher2
$ git apply --check ~/changes/ginger-patch/launcher2
$ git apply ~/changes/ginger-patch/launcher2

After you have applied these patches, rebuild the system
image as usual.

Note:  These patches changes the Android API, so the second
time you do a make you'll get a message that the API has
changed.  Follow the instructions in that message for making
a new API.


H i s t o r y
=============
The remaing part of this file describes how to obtain the
Android source, build the system.img, run the emulator with
the system.img, etc.  It also describes how the patch files
were created.

How to Get the Android Source 
=============================
(http://source.android.com/source/download.html)

The directory ~windriver/android/ginger-build is our "Gingerbread Home" and was
created as follows:

$ cd ~windriver/android
$ mkdir ginger-build
$ cd ginger-build
$ repo init -u git://android.git.kernel.org/platform/manifest.git -b android-2.3.5_r1

How to Build the Emulator System.img
===================================
The vanilla emulator system image is then built with the following commands
(still in ~windriver/android/ginger-build):

$ source build/envsetup.sh
$ lunch generic-eng
$ make

When the make finishes, the system image will be in the file:
out/target/product/generic/system.img

How to Execute the System.img in the Emulator
=============================================
After creating an AVD named "ginger" (use the SDK tool 'android' to do this), the
emulator is run with (still in ~windriver/android/ginger-build):

$ emulator -system out/target/product/generic/system.img -avd ginger


TDK Patch Creation
===================
The TDK team modified some sources files in gingerbread to make
Android testable.  Once these changes were thoroughly tested
a diff file was created by going into the gingerbread home
directory: 

$ cd ~windriver/android/gingerbread
$ repo diff > patch.diff

The files frameworks, launcher2, gallery3d, camera, and gallery
were created by breaking apart patch.diff on project boundaries.
