12/13/2011
Wind River
Ice Cream Sandwich
Draft 1


These patches have been tested with Ice Cream Sandwich
[ICS] 4.0.1 (-b android-4.0.1_r1).

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
and Froyo. [required]

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


contacts
--------
The retail version of the Contacts app will bring
up a dialog if you have no contacts asking you to
add one.  The Contacts app built from AOSP source
does not do this.  Further, the menu item for
adding a new contact only appears on the Contacts
app for a few seconds after the first launch of
the Contacts app after the phone is booted.
If you navigate away from the screen with the
"add contact" menu and then back, the menu will
be gone and will not show up until next reboot.

This is probably a bug. In order for the
UXTDK Contacts app tests to work we require more
normal behavior.  The contacts app patches will
prevent the "add contact" menu from disappearing.

A human using the phone can work around this by
quickly entering a single contact on first use
of Contacts after reboot.  The add contacts menu
will always appear after that.

If you want to run the UXTDK contacts tests
you must have a patch like this in place.  The
tests delete all contacts multiple times and
that will make the menu disappear.

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

contacts
--------
$ cd $iHome
$ cd packages/apps/Contacts
$ git apply --check $iPatch/contacts
$ git apply $iPatch/contacts

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

The directory ~windriver/android/ics-build is our "ICS Home" and was
created as follows:

$ cd ~windriver/android
$ mkdir ics-build
$ cd ics-build
$ repo init -u https://android.googlesource.com/platform/manifest -b android-4.0.1_r1

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
$ repo diff > ics-r4.diff

The files frameworks, launcher2, settings, device, and contacts
were created by breaking apart ics-r4.diff on project boundaries.
