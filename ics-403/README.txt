1/21/2012
Wind River
Ice Cream Sandwich
Draft 2

I M P O R T A N T   N O T E
===========================
The frameworks API has been redesigned so these
patches no longer alter the Android API.  If you
apply these patches to a fresh download (repo sync)
of the AOSP source, this change should be transparent
to you.

If you have the previous ICS patches and want to
mix a portion of these new patches (for example, just
one app) with existing patches, then you will have
to do some extra work.  You should consult the wiki
(http://code.google.com/p/android-app-testing-patches/w/list)

Current Status
==============
These patches have been tested with Ice Cream Sandwich
[ICS] 4.0.3 (-b android-4.0.3_r1).

At the time this patch set was created, we are working
on making two apps more testable: Email and Calendar.
THIS WORK IS NOT FINISHED!  It is almost certain that
the patches for these two apps will be changing in the
near future.

If some of the patches do not apply to your AOSP source
tree, all is not lost -- they are usually really easy
to apply by hand.

Notes on ICS patches
====================
You should review these descriptions.  You may not
require all these patches.

frameworks
----------
The frameworks patches are very similiar to Gingerbread
and Froyo. [required].  To eliminate changes to the
Android API, we had to make ViewDebug.DumpClass private.
To expose the methods of DumpClass, we added methods in
ViewDebug.  These new methods are annotated with @hide.

launcher2
---------
Some extra patching for Launcher2 has been
required because the launcher supports multiple screens
of apps and widgets, but gives no reliable indication
about which of these screens is currently visible.
[required]

settings
--------
The settings app has certain places where it tests
to see if monkey is running, and if so it silently
prevents the setting.  For example, if monkey is
running on your ICS phone and you:

  1.  bring up the Settings app
  2.  scroll near the bottom and select
      "{ } Developer options"
  3.  click "Stay awake"

You will see a green checkmark.  But:

  4. click "back" to the main Settings screen
  5. select "{ } Developer options" again

The checkmark will not be there.

The UXTDK agent, once connected to a phone, will
detect if the phone goes offline (e.g., to be
flashed) and when it comes back online the agent
will start monkey if monkey is not already running.
Before the phone has finished booting, monkey
will be running.

You can avoid the need for this patch by always
making sure a UXTDK agent is not connected to
your phone whenever the phone is rebooted after
flashing -- once you successfully set
"Stay awake", it will remain set until either
user data is wiped or the phone is reflashed.

device
------
This patch was required so fastboot would flash
the phone.  Otherwise, it gave an error about
version mismatch.

If you do not get the error with fastboot or
if you are using an alternative to fastboot
(e.g., Odin), you may not need this patch.


How to Apply these Patches
==========================
The patches are applied as follows, assuming
~/android/ics-build is the directory where you did the 
original "repo init" for ICS and ~/changes/ics-patch
is where you unpacked these patch files:

$ export iHome=~/android/ics-build
$ export iPatch=~/changes/ics-patch

frameworks
----------
$ cd $iHome
$ cd frameworks/base
$ git apply --check $iPatch/frameworks
$ git apply $iPatch/frameworks

launcher2
---------
$ cd $iHome
$ cd packages/apps/Launcher2
$ git apply --check $iPatch/launcher2
$ git apply $iPatch/launcher2

device
------
$ cd $iHome
$ cd device/samsung/maguro
$ git apply --check $iPatch/device
$ git apply $iPatch/device

settings
--------
$ cd $iHome
$ cd packages/apps/Settings
$ git apply --check $iPatch/settings
$ git apply $iPatch/settings

browser
-------
$ cd $iHome
$ cd packages/apps/Browser
$ git apply --check $iPatch/browser
$ git apply $iPatch/browser

gallery2
--------
$ cd $iHome
$ cd packages/apps/Gallery2
$ git apply --check $iPatch/gallery2
$ git apply $iPatch/gallery2

email  (still under development)
-----
$ cd $iHome
$ cd packages/apps/Email
$ git apply --check $iPatch/email
$ git apply $iPatch/email

calendar (still under development)
--------
$ cd $iHome
$ cd packages/apps/Calendar
$ git apply --check $iPatch/calendar
$ git apply $iPatch/calendar



After you have applied these patches, rebuild the system
image as usual.


H i s t o r y
=============
The remaing part of this file describes how to obtain the
Android source, build the system.img, run the emulator with
the system.img, etc.  It also describes how the patch files
were created.

How to Get the Android Source 
=============================
(http://source.android.com/source/download.html)

The directory ~windriver/android/ics-build is our "ICS Home" and was
created as follows:

$ cd ~windriver/android
$ mkdir ics-build
$ cd ics-build
$ repo init -u https://android.googlesource.com/platform/manifest -b android-4.0.3_r1

How to Build the Emulator System.img
===================================
The vanilla emulator system image is then built with the following commands
(still in ~windriver/android/ics-build):

$ source build/envsetup.sh
$ lunch full-eng
$ make

When the make finishes, the system image will be in the file:
out/target/product/generic/system.img

How to Execute the System.img in the Emulator
=============================================
After creating an AVD named "ics1" (use the SDK tool 'tools/android' to do
this), the emulator is run with (still in ~windriver/android/ics-build):

$ emulator -system out/target/product/generic/system.img -avd ics1

Alternatively you can copy the system.img file to your avd directory,
~/.android/avd/ics1.avd.  Then you can launch it from the tools/android
SDK tool by selecting it and clicking "Start...".

TDK Patch Creation
===================
The TDK team modified some sources files in ICS to make
Android testable.  Once these changes were thoroughly tested
a diff file was created by going into the ICS home directory: 

$ cd ~windriver/android/ics-build
$ repo diff > 1.0_p3.diff

The files browser, calendar, device, email, frameworks, gallery2,
launcher2, and settings were created by breaking apart 1.0_p3.diff
on project boundaries.

