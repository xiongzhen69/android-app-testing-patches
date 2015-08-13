[Overview](AndroidAppTestingPatches.md)
## Patch Details ##
The patches are divided into the categories described below.  The patches are not that complex.  The details here will make more sense if you are looking at the code.  Download the patches themselves (click the Source link above) and either look at them as-is or apply the patches to Android and then examine the resulting files using [Eclipse](http://source.android.com/source/using-eclipse.html) (strongly recommended).
## Notes on code execution ##
The code introduced into Android by the patches does not execute during normal Android device operation.  It only executes if a DUMPQ command is received by ViewServer.  Note that a retail device will not have ViewServer enabled.  Android must be built as an engineering build (ro.secure=0, ro.debuggable=1, ro.kernel.android.checkjni=1) for ViewServer to be enabled.
### Frameworks ###
These patches are to the Android view structure.  ViewServer and ViewDebug are modified to support the **dumpClass** mechanism and DUMPQ command described in the overview.  The View objects, View, ViewGroup, and many widgets have _dumpClass_ added so GUIs composed of these objects automatically expose GUI information in the DUMPQ command, just like they do for the DUMP command.

**Note for ICS-403:** The parameter signature for _dumpClass_ has changed.  Please see [NewPatchArchitecture2012](NewPatchArchitecture2012.md).

### Launcher2 ###
The 3D launcher has an OpenGL-based GUI.  We have added the necessary _dumpClass_ routines to allow automated testing to launch apps through the GUI.  This allows an automated testing system to launch the app being tested or test common use cases that involve multiple apps (e.g.,
### Gallery3d, Gallery, Camera ###
These are 3 apps with OpenGL-based GUI structures.  The _dumpClass_ routines have been added to the appropriate GUI classes to allow them to be automatically tested.