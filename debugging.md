
# Debug AOSP system process (Java)

http://ronubo.blogspot.com/2016/01/debugging-aosp-platform-code-with.html 

 * mmm development/tools/idegen && development/tools/idegen/idegen.sh 
 * open android.ipr (in root dir) from Android Studio 
 * let android index/parse the code (takes a long time >30 minutes) 
 * in android studio, create a new configuration of Android App type, give it a name, it doesn’t matter what 
 * now you can attach to debugger like a normal app and debug system_process, etc. 


## Remount Filesystem RW/RO 
```
mount -o rw,remount /system 
```
 

## Kernel Driver Debugging 

Add `#define DEBUG 1` to driver file 
```
dmesg –n 8 or echo 8 > /proc/sys/kernel/printk 
```

## Debug Native Process 

https://source.android.com/devices/tech/debug/gdb 

Debugging native process with VSCode: 
```
# gdbdebug.py –r <name_of_exe> --setup-forwarding vscode 
```
This will start the exe on the target and print out vscode debug config, cut-paste into VSCode launch.json and hit F5 to start debugging. Gdbdebug.py can all attach to a running process, checkout the --help option for details 


https://www.gamedev.net/articles/programming/general-and-gameplay-programming/android-debugging-with-visual-studio-code-r4820/

### Adding Debug Trace Logging 

AOSP comes with nice function level tracing. Add to your code: 
```
#include <log/log.h>
#define ATRACE_TAG (ATRACE_TAG_APP) 
#include <utils/Trace.h>     <== for C++ 
#include <cutils/trace.h>    <== for C  

Void myclass::foo() { 
  ATRACE_CALL(); 
  ... 
}

Void cfunc() { 
  ATRACE_BEGIN("cfunc"); 
  ... 
  ATRACE_END() 
}
```

You can then use atrace tool on the device to view all the trace logs: 
```
# atrace –a <app_name> --stream 
```
Where app_name is the name of the native executable. If you want to ensure you always see everything, you can use the `ATRACE_TAG_ALWAYS` instead of `ATRACE_TAG_APP` 

Note that adding `ATRACE_CALL()` uses `__builtin_expect()` (gcc branch prediction) So it shouldn't have too much of an impact on performance 

### Systrace 
https://source.android.com/devices/tech/debug/systrace 
https://developer.android.com/studio/command-line/systrace#shortcuts 

Systrace utility uses the same ATRACE_* but generates a nice visual: 

Monitor <app_name> for 10 seconds and then write a html file 
```
systrace.py -a <app_name> -t 10
```
