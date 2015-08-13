## Troubleshooting Guide ##
You have applied the patches, but they do not seem to be working.  What to do?
<ol>
<li> Did you rebuild the Android system.img and reflash the device with the new system.img?  Are you sure?<br>
<li><b>adb logcat</b>. The <i>frameworks</i> patch contains a change to ViewServer.java that logs so you can see it from <i>adb logcat</i>.  When you are executing a UXTDK test you should see lines like this in the logcat output (Galaxy Nexus running ICS 4.0.3):<br>
<pre>
D/ViewServer(  219): DUMPQ 41b3a580<br>
D/ViewServer(  219): DUMPQ-done 135<br>
D/ViewServer(  219): DUMPQ ffffffff<br>
D/ViewServer(  219): DUMPQ-done 64<br>
</pre>
<ol />
The DUMPQ line is logged when ViewServer gets the DUMPQ request.  The line contains the window id (in hex) whose dump is being requested.  All f's means "the current focus window".<br>
<br>
The DUMPQ-done line is logged after everything is complete.  The number on this line is the number of milliseconds it took to perform the dump.<br>
<br>
<li><b>stack dump in logcat</b>.  If you see a stack dump in the logcat output that has ViewServer or ViewDebug in it, that could be a problem created by the patches.  Look carefully at the code indicated by the stack dump.<br>
<br>
<li><b>devicelogs</b> (examine dump files for sanity).  The purpose of the DUMPQ command is to deliver a description of the GUI objects in a window to the UXTDK agent.  The agent writes each dump to a separate file in a tdk/devicelogs/MM-dd-hh-mm/ directory (MM = month, dd = day, hh = hour, mm = minute).  The directory is created when the agent is started and the agent uses this directory for as long as it is executing.  Each agent DUMPQ command will result in a DUMPQ file.  Here is an excerpt from the agent's log of a test:<br>
<pre>
2012-03-29 10:54:24,643 [TDKService] DEBUG|ACTION: findTopWindow: [0](), [1]50<br>
2012-03-29 10:54:24,644 [ViewHierarchyLoader] INFO |==> DUMPQ ffffffff<br>
2012-03-29 10:54:24,645 [ViewHierarchyLoader] INFO |waiting for in.ready<br>
2012-03-29 10:54:24,746 [ViewHierarchyLoader] INFO |==> GET_FOCUS<br>
2012-03-29 10:54:24,792 [ViewHierarchyLoader] INFO |Focus Window: 41b3a580 com.android.launcher/com.android.launcher2.Launcher<br>
2012-03-29 10:54:24,792 [ViewHierarchyLoader] INFO |com.android.internal.policy.impl.PhoneWindow$DecorView@4168eef8: (0, 0)<br>
2012-03-29 10:54:24,823 [ViewHierarchyLoader] INFO |}=> DONE 12<br>
2012-03-29 10:54:24,823 [TDKService] DEBUG|{findTopWindow} returning (3): [0]0 [1]1 [2]INFO:(dumpFile: 12_dumpq.txt)<br>
</pre>
In this excerpt we see that the agent is issuing a DUMPQ command (DUMPQ ffffffff).  A few lines from the bottom you see:  <b>DONE 12</b> which gives the number of the DUMPQ file, 12_dumpq.txt, in the agent's devicelogs/MM-dd-hh-mm directory where this GUI dump will be written.<br>
<br><br>
You can edit these nnn_dumpq.txt files to see if it looks ok.  It contains many really long lines.  If you are having problems with the patches you may be requested to deliver the entire directory to Wind River personnel.  Each line represents a GUI object in the window.  Each line should have at least these elements:<br>
<br>
<ul><li>screenX<br>
</li><li>screenY<br>
</li><li>getHeight()<br>
</li><li>getWidth()<br>
</li><li>getVisibility()<br>
</li><li>mLeft<br>
</li><li>mTop</li></ul>

Each line begins with the pattern: <i>dotted-name@hex-number</i>.  For example: android.widget.FrameLayout@40599930.   If you see a dotted-name pattern in the middle of a line (except the first line), this is almost for certain an error.<br>
<li><b>Use HierarchyViewer</b>.  UXTDK contains a modified HierarchyViewer.jar that will use DUMPQ.  Follow these steps:<br>
<ol><li>copy HierarchyViewer.jar to an empty directory on the computer connected to the Android device<br>
</li><li>Navigate the phone (using your fingers) to the window you want to dump.<br>
</li><li>Execute HierarchyViewer:  java -jar HierarchyViewer.jar<br>
</li><li>click the Devices button in HierarchyViewer<br>
</li><li>select the Window (in HierarchyViewer) that corresponds to the window you navigated to above.<br>
</li><li>click the "Load View HierarchyQ" button in HV.</li></ol>

This will display a graphical hierarchy picture of the GUI objects in this window.  It will also create a dump file in a new <b>devicelogs</b> directory that HierarchyViewer will create.  This gives you the ability to perform one DUMP and see one dump file.