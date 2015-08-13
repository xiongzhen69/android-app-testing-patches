## GUI object tree dump interface for automatic testing ##
Support automatic use-case testing with a reasonable-speed dump (DUMPQ) of an activity's view hieararchy.  The dump format is compatible with [HieararchyViewer](http://developer.android.com/guide/developing/debugging/debugging-ui.html)'s DUMP ("loadGraph" button).  This implementation has the following attributes:

  1. Reasonable performance - Automated testing needs to be 20 to 40x faster than the HieararchyViewer/ViewDebug interface
  1. Extensible - Apps that use OpenGL can expose their GUI hierarchy through this interface and therefore can be tested
  1. Reliability - every dump request must return something, even if only an error indication
  1. Absolute position - test operations through Monkey require the absolute position of a GUI object on the screen
  1. Clipboard access - makes clipboard available with each dump.  This is critical for browser testing.

To measure performance we modified HierarchyViewer (not included in this patch) to invoke and measure the round trip time of a DUMP and DUMPQ request.  The performance is typically 20 to 50x faster than HieararchyViewer.

The patch includes use of the extensibility mechanism to make the GUI of the 3D Launcher, camera, and the 3D Gallery apps available for automated testing (also for limited viewing through HierarchyViewer).

**Use the [Source](http://code.google.com/p/android-app-testing-patches/source/checkout) tab above to download the patches.**  Just checkout the source and you'll have everything.