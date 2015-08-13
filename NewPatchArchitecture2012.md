This document pertains to the ICS-403 patch set made available on Jan 21, 2012.
## ICS Patch Architecture Change ##
The previous version of the ICS patches added methods and a class to the Android API.  We have redesigned a portion of the patches so this is no longer necessary

## Details ##

### dumpClass ###
**dumpClass** is the routine called by ViewDebug to dump a view.  It's signature has changed:
```
/*new:*/  protected void dumpClass(ViewDebug vdp) {...}
/*old:*/  protected void dumpClass(ViewDebug.DumpControl dp) {...}
```
(Changing the formal parameter name from "dp" to "vdp" is not significant.)  All the methods available through ViewDebug.DumpControl are now available through ViewDebug directly.  However, all the methods have an @hide annotation to prevent them from being entered into the Android API.

### Working Around @hide ###
If a method in Android is annotated with @hide, then it can be called from classes inside Android but (in theory) not by apps.  For example the method _ViewDebug.writeEntry_, that dumps the property of a view, can be called from classes in Android's frameworks/base project, but should not be visible to apps.

You can work around this by commenting out this line from the app's _Android.mk_ makefile:
```
#LOCAL_SDK_VERSION := current
```
This tells the Android build system that the app is to be compiled against Android before the @hide annotations take effect.  This does require that the app be part of the Android build system.