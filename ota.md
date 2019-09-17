## Create a full update file
```
$ m
$ m dist 
```

This command creates a bunch of things found in `out/dist` dir. The most important thing is the `*-target_files-*.zip`. This file contains everything to create full or incremental OTA files. The .apks within the target_files.zip are signed with the *test-keys* (found under `build/target/product/security`). Before making a real distribution for your device, it is important to sign everything using your own *release-keys* that only you have access to.

The `dist` build run release tools found build/tools/releasetools. The release tools actually reads the recovery.fstab file in LoadRecoveryFSTab so be sure that the recovery.fstab has entries for /system and others.

 * https://www.protechtraining.com/blog/post/341
 * https://cfig.github.io/2015/09/22/OTA-diff-and-patch/

## Create release keys

see: https://source.android.com/devices/tech/ota/sign_builds#release-keys

```
$ subject='/C=US/ST=California/L=Mountain View/O=Android/OU=Android/CN=Android/emailAddress=android@android.com'
$ mkdir ~/.android-certs
$ for x in releasekey platform shared media; do \
    ./development/tools/make_key ~/.android-certs/$x "$subject"; \
  done
```

Once you generate your special release keys, you can import them into a java keyfile. This is useful when using Android Studio developement on apps. Example how to import the platform key:

```
keystore.jks: videray-certs/platform.pk8 videray-certs/platform.x509.pem
	./keytool-importkeypair -k $@ -p platform -pk8 videray-certs/platform.pk8 -cert videray-certs/platform.x509.pem -alias platform -passphrase videray
```

the `keytool-importkeypair` script can be found [here](files/keytool-importkeypair)

## Create signed-target_files.zip

```
$ ./build/tools/releasetools/sign_target_files_apks \
    -o \
    --default_key_mappings ~/.android-certs out/dist/*-target_files-*.zip \
    signed-target_files.zip
```

## Create Full OTA package

```
$ ./build/tools/releasetools/ota_from_target_files \
    --key_mapping ~/.android-certs/releasekey \
    signed-target_files.zip \
    signed-ota_update.zip
```

## Create Incremental OTA package

```
./build/tools/releasetools/ota_from_target_files -i PREVIOUS-tardis-target_files.zip dist_output/tardis-target_files.zip incremental_ota_update.zip
```


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

## Sideloading ##

You can now install the OTA manually by rebooting the device into recovery mode, choose "Apply updates from ADB", and then running "adb sideload /path/to/OTA.zip"

Signing OTA packages for sideloading:

```
java -Djava.library.path="out/host/linux-x86/lib64" -jar out/host/linux-x86/framework/signapk.jar -w /data/projects/android-firmware-build/videray-certs/releasekey.x509.pem /data/projects/android-firmware-build/videray-certs/releasekey.pk8 inc_flintlock_beta_0_4_30-flintlock_beta_0_4_32.zip signed.zip
```
http://jhshi.me/2013/12/02/how-to-create-and-sign-ota-package/index.html#.XXlbRHVKglU

https://source.android.com/devices/tech/ota/nonab/inside_packages#edify-syntax

 * Reboot into recovery mode, select option to side load via ADB
 * From computer `adb sideload signed.zip`


## Resouces

 * http://solarex.github.io/wiki/Android/android_ota_update.html
 * https://source.android.com/devices/tech/ota/sign_builds