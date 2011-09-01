2/8/2011
Wind River

How to Apply these Patches
==========================
The patches are applied as follows, assuming
~/android/ginger_r1 is the directory where you did the 
original "repo init" for gingerbread r1 and 
~/changes/ginger_r1 is where you unpacked these files:

frameworks
----------
$ cd ~/android/ginger_r1
$ cd frameworks/base
$ git apply --check ~/changes/ginger_r1/frameworks
$ git apply ~/changes/ginger_r1/frameworks

gallery
-------
$ cd ~/android/ginger_r1
$ cd packages/apps/Gallery
$ git apply --check ~/changes/ginger_r1/gallery
$ git apply ~/changes/ginger_r1/gallery

launcher2
---------
$ cd ~/android/ginger_r1
$ cd packages/apps/Launcher2
$ git apply --check ~/changes/ginger_r1/launcher2
$ git apply ~/changes/ginger_r1/launcher2

After you have applied these patches, rebuild the system
image as usual.

Note:  These patches changes the Android API, so the second
time you do a make you'll get a message that the API has
changed.  Follow the instructions in that message for making
a new API.


system.img
==========
The system.img file included as part of this package has been
built with the patches and is ready to run in the emulator.
See "How to Execute the System.img in the Emulator" below.



H i s t o r y
=============
The remaing part of this file describes how to obtain the
Android source, build the system.img, run the emulator with
the system.img, etc.  It also describes how the patch files
were created.

How to Get the Android Source 
=============================
(http://source.android.com/source/download.html)

The directory ~windriver/android/ginger_r1 is our "Gingerbread R1 Home" and was
created as follows:

$ cd ~windriver/android
$ mkdir ginger_r1
$ cd ginger_r1
$ repo init -u git://android.git.kernel.org/platform/manifest.git -b android-2.3.1_r1

How to Build the Emulator System.img
===================================
The vanilla emulator system image is then built with the following commands
(still in ~windriver/android/ginger_r1):

$ source build/envsetup.sh
$ lunch generic-eng
$ make

When the make finishes, the system image will be in the file:
out/target/product/generic/system.img

How to Execute the System.img in the Emulator
=============================================
After creating an AVD named "ginger" (use the SDK tool 'android' to do this), the
emulator is run with (still in ~windriver/android/ginger_r1):

$ emulator -system out/target/product/generic/system.img -avd ginger


STP Patch Creation
===================
The STP team modified some sources files in ginger_r1 to make
Android testable.  Once these changes were thoroughly tested
the file patch.diff was created by going into the ginger_r1
home directory: 

$ cd ~windriver/android/ginger_r1
$ repo diff > patch.diff

The files frameworks, launcher2, and gallery were 
created by breaking apart patch.diff on project boundaries.
