## Create a full update file
```
$ m
$ m dist 
```

This command creates the OTA .zip file found in dist_output/flintlock-ota.zip. You can now install the OTA manually by rebooting the device into recovery mode, choose "Apply updates from ADB", and then running "adb sideload /path/to/OTA.zip"

The dist build run release tools found build/tools/releasetools. The release tools actually read the recovery.fstab file in LoadRecoveryFSTab so be sure that the recovery.fstab has entries for /system and others.

 * https://www.protechtraining.com/blog/post/341
 * https://cfig.github.io/2015/09/22/OTA-diff-and-patch/

## System signing Keys and Signing OTA
Read towards the bottom of: http://solarex.github.io/wiki/Android/android_ota_update.html


## Reboot Procedure
From the android command line, you can reboot the device by issuing: 
```
# reboot 
 -- or --
# reboot bootloader
```

the Reboot command code is found in the project system/core/reboot when running reboot with the "bootloader" argument, the reboot command will set the "sys.powerctl" property value to "bootloader"

The property change is handled in the [init process](http://androidxref.com/8.1.0_r33/xref/system/core/init/init.cpp#178) which then calls [HandlePowerctlMessage](http://androidxref.com/8.1.0_r33/xref/system/core/init/reboot.cpp#466), which then calls [write_reboot_bootloader](http://androidxref.com/8.1.0_r33/xref/bootable/recovery/bootloader_message/bootloader_message.cpp#194) which writes the string "bootonce-bootloader" into the misc partition.

## Updater ##

'Updater' is the name of exe that runs the updater script that does the actual work of applying the update.

https://source.android.com/devices/tech/ota/nonab/device_code#updater
