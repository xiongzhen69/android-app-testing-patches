# Introduction #

The purpose of these Android source patches is to make automated use-case testing on Android devices feasible.  Automated use-case tests interact with the device exactly like a human, by issuing click, enter text, scrolling, etc, commands supported by the device and examining the screen to see if the expected result appeared.

The patches focus on the "examining the screen" aspect of use-case testing.  Android has built into it an interface for the [HierarchyViewer](http://developer.android.com/guide/developing/tools/hierarchy-viewer.html) tool that dumps the GUI structure of a window if that structure is "[View-based](http://developer.android.com/guide/topics/ui/index.html)".  The patches dramatically speed up this interface.  The patches also introduce a mechanism so apps with OpenGL-based GUIs can expose their GUI structure for testing.  Finally, the patches use the mechanism to expose the GUIs of some OpenGL Android apps: the 3D launcher, gallery, and the camera.

PatchDetails

# Overview #

## Speed ups ##
HierarchyViewer communicates with the ViewServer service on an Android device by sending commands over a socket.  The DUMP command retrieves the GUI Hierarchy, but it is very slow (see [this](http://groups.google.com/group/android-contrib/browse_thread/thread/de5b348a07bf13f9)).  These patches introduce a new command, DUMPQ, that is 20x - 40x faster.

The Android GUI is built on the View and ViewGroup objects (described [here](http://developer.android.com/guide/topics/ui/index.html)).  Java annotation from the ViewDebug class is used to declare which member fields and methods should be dumped for display by HierarchyViewer.  ViewDebug implements the DUMP command by walking the GUI tree of objects using Java introspection to discover which members are annotated and then obtaining and writing their values to the socket.

The new DUMPQ command implementation dumps member fields and method values in exactly the same format as DUMP.  DUMPQ avoids introspection as much as possible.  The implementation, which these patches enter into ViewDebug, walks the GUI tree examining each object for a member method named **dumpClass** that is called to write the member values to the socket.  Introspection is avoided by putting the object's dumpClass routine in the object itself.  It is introspection that is the primary source of slowness in the implementation of DUMP.

A second speedup comes by only dumping the member fields critical for automated testing.  ViewDebug's DUMP command is focused on supplying detailed information about GUI objects to HierarchyViewer.  The DUMPQ command is focused just on testing.

## OpenGL apps can expose their GUI for testing ##
A second benefit of this design, where a routine directly in the GUI object is called to dump its own structure, is it naturally allows for GUIs implemented by OpenGL to expose their GUI structure for testing.  All that is required is to supply a _dumpClass_ routine in the correct GUI objects.

## Patch some OpenGL apps to expose their GUI ##
We have patched the 3D launcher, gallery, and the camera OpenGL apps so they can utilize the DUMPQ command and be automatically tested.

These can be used as examples for how to expose OpenGL GUI data for testing.

PatchDetails

# Additional Changes #
## Exception trapping ##
The patches try to trap every exception that can occur.  This way a DUMPQ command will always receive at least a response with one line: DONE.  Otherwise automated testing had to wait for socket timeout to determine that an error had occurred.
## Absolute window location ##
Almost every window will have two new fields added, **screenX** and **screenY**.  These two fields contain the absolute position of the upper left corner of the window.  screenY can be negative, e.g., if the window is pushed up by the addition of the on-screen keyboard.
## Browser testing support ##
The Android browser does not expose any GUI info suitable for testing.  The patches add just enough to make it possible to test the browser.  The contents of the clipboard, if not null, are returned in the top-level window of the DUMPQ output as the field **clipboardText**.  Testing can use monkey to select text and copy it to the clipboard.
## Logcat ##
DUMPQ command processing is recorded in logcat.  When ViewServer receives the DUMPQ command it logs it.  When ViewDebug is finished and control passes back to ViewServer, "DUMPQ-done" will be displayed and the time, in milliseconds, it took to process the command.
## HiearchyViewer ##
The output of DUMPQ is compatible with HierarchyViewer's DUMP command so HierarchyViewer can display the DUMPQ without modification.
We have modified HierarchyViewer, by adding a new button to do DUMPQ in addition to DUMP (i.e., "Load View HierarchyQ" in addition "Load View Hierarchy").