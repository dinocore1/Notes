## 2019-09-17

Working on android app permissions. AOSP builds can include a privapp-permission-DEVICE_NAME.xml to grant system-level privileges to app.


privapp-permissions-flintlock.xml
```
<?xml version="1.0" encoding="utf-8"?>
<permissions>

    <privapp-permissions package="com.videray.systemupdater">
        <permission name="android.permission.ACCESS_CACHE_FILESYSTEM"/>
        <permission name="android.permission.WRITE_EXTERNAL_STORAGE" />
        <permission name="android.permission.ACCESS_DOWNLOAD_MANAGER" />
        <permission name="android.permission.ACCESS_DOWNLOAD_MANAGER_ADVANCED" />
        <permission name="android.permission.REBOOT"/>
        <permission name="android.permission.RECOVERY"/>
    </privapp-permissions>

    <privapp-permissions package="com.videray.sparklauncher">
        <permission name="android.permission.BIND_APPWIDGET" />
        <permission name="android.permission.GET_ACCOUNTS_PRIVILEGED" />
    </privapp-permissions>

</permissions>
```

```
PRODUCT_COPY_FILES += \
	device/videray/flintlock/privapp-permissions-flintlock.xml:$(TARGET_COPY_OUT_VENDOR)/etc/permissions/privapp-permissions-flintlock.xml \
```

https://source.android.com/devices/tech/config/perms-whitelist

## 2019-09-16

Working on screen blank bug today. I think that the problem is that when the screen comes back on, sometimes the screen is out-of-sync. Theory: this is because abigail never pulls power from the screen so the LVDS receiver never looses state. However, the SoM totally looses state.

Potential fix:
  * __Fix 1:__ It does not seem that Abigail is currently pulling power from the screen when the power is pressed. Experment with pulling power, maybe that will fix everything
  * __Fix 2:__ Hack the KMS android display driver to never send the signal that poweroffs the display
  
```
diff --git a/display/display/KmsDisplay.cpp b/display/display/KmsDisplay.cpp
index 0a7138f..387f96f 100644
--- a/display/display/KmsDisplay.cpp
+++ b/display/display/KmsDisplay.cpp
@@ -386,6 +386,10 @@ int KmsDisplay::setPowerMode(int mode)
         return 0;
     }
 
+    if(mPowerMode != DRM_MODE_DPMS_ON) {
+        return 0;
+    }
+
     int err = drmModeConnectorSetProperty(mDrmFd, mConnectorID,
                   mConnector.dpms_id, mPowerMode);
     if (err != 0) {

```



## 2019-09-12

Workign on a problem in the Android recovery mode when tring to perform a FPGA flash. There is some error with SPI transfer. I noticed that the linux boot logs also showed an error with the SPI device complaining that the SPI DMA firmware is not ready. Turns out I needed to add the `imx/sdma/sdma-imx7d.bin` firmware to the recovery's initram image under `/lib/firmware`.

```
imx-sdma 30bd0000.sdma: sdma firmware not ready!
```

## 2019-09-11

Signing OTA packages for sideloading:

```
java -Djava.library.path="out/host/linux-x86/lib64" -jar out/host/linux-x86/framework/signapk.jar -w /data/projects/android-firmware-build/videray-certs/releasekey.x509.pem /data/projects/android-firmware-build/videray-certs/releasekey.pk8 inc_flintlock_beta_0_4_30-flintlock_beta_0_4_32.zip signed.zip
```
http://jhshi.me/2013/12/02/how-to-create-and-sign-ota-package/index.html#.XXlbRHVKglU

https://source.android.com/devices/tech/ota/nonab/inside_packages#edify-syntax



## 2019-07-09


```
adb logcat -v time -b radio
```

```
adb shell setprop sys.ril.type QMI
```

### AT Mode

```
busybox microcom -s 115200 /dev/ttyUSB2
AT
AT!GSTATUS?
AT+COPS?
AT!SCACT=1,1
```

#### LTE

The modem was only connecting to 3g when it came outta the box. This seemed to fix it:

https://forum.sierrawireless.com/t/mc7455-t-mobile-cannot-connect-when-band-09-lte-all/14469


at+cops=?
+cops: (1,"T-Mobile","T-Mobile","310260",7),(1,"312 530","312 530","312530",7),(1,"311 882","311 882","311882",7),(1,"AT&T","AT&T","310410",7),(1,"FirstNet","FirstNet","313100",7),(1,"Sprint","Sprint","310120",7),(1,"Verizon","Verizon","311480",7),,(0,1,2,3,4),(0,1,2)

OK
at+cops=1,2"310260",7
ERROR
at+cops=1,2,"310260",7
OK
at!priid?
PRI Part Number: 9907344
Revision: 002.001
Customer: Generic-M2M

Carrier PRI: 9999999_9907258_SWI9X50C_01.07.02.00_00_ATT_002.008_004
Carrier PRI: 9999999_9907259_SWI9X50C_01.09.04.00_00_GENERIC_002.019_000

OK
at!gstatus?
!GSTATUS: 
Current Time:  4306		Temperature: 35
Reset Counter: 2		Mode:        ONLINE         
System mode:   LTE        	PS state:    Attached     
LTE band:      B66    		LTE bw:      20 MHz  
LTE Rx chan:   0		LTE Tx chan: 132272
LTE SSC1 state:NOT ASSIGNED
LTE SSC2 state:NOT ASSIGNED
LTE SSC3 state:NOT ASSIGNED
LTE SSC4 state:NOT ASSIGNED
EMM state:     Registered     	Normal Service 
RRC state:     RRC Idle       
IMS reg state: No Srv  		

PCC RxM RSSI:  -87		PCC RxM RSRP:  -119
PCC RxD RSSI:  -55		PCC RxD RSRP:  -86
Tx Power:      --		TAC:         550d (21773)
RSRQ (dB):     -11.6		Cell ID:     00a51b02 (10820354)
SINR (dB):     12.2



REQUEST_SET_PREFERRED_NETWORK_TYPE error: com.android.internal.telephony.CommandException: MODE_NOT_SUPPORTED ret=

## 2019-05-17

Working on implementing OTA updates. The bootloader (u-boot) boots into recovery mode when either: (1) the users is pressing a special hardware button or (2) reading data from the 'misc' partition.


The Android system writes 'boot-recovery' into the misc partition when it has finished downloading an OTA update file and now wishes to reboot into recovery mode. The bootloader should therefor read the misc partition and look for the special 'boot-recovery' command and if found, boot into recovery mode. In uboot, booting into recovery mode is performed by running the `boota recovery` command. Reading the misc parition is handled by `f_fastboot.c::fastboot_get_bootmode` and then `bcb_read_command`. Note: both `CONFIG_BCB_SUPPORT` and `CONFIG_ANDROID_RECOVERY` need to be defined.

## 2019-05-13

AOSP creates a GPT image as part of the build if `BOARD_BPT_INPUT_FILES` is defined in BoardConfig.mk. The tool that builds the image is system/tools/bpt. NXP forked and added the `first_partition_offset` feature. Later, AOSP added its own simular feature but named it `partitions_offset_begin`.

```
BOARD_BPT_INPUT_FILES += device/videray/flintlock/flintlock-partitions.bpt
```
bpt is a json file:
```
{
    "settings": {
        "disk_size": "15930 MB",
        "disk_alignment": 512,
        "partitions_offset_begin": "33 KiB"
    },
    "partitions": [
        {
            "label": "bootloader",
            "size": "2 MiB"
        },
        {
            "label": "boot",
            "size": "32 MiB"
        },
        {
            "label": "recovery",
            "size": "32 MiB"
        },
        {
            "label": "system",
            "size": "1700 MiB"
        },
        {
            "label": "vendor",
            "size": "200 MiB"
        },
        {
            "label": "cache",
            "size": "512 MiB"
        },
        {
            "label": "misc",
            "size": "1 MiB"
        },
        {
            "label": "userdata",
            "size": "6 GiB"
        },
        {
            "label": "fbmisc",
            "size": "128 KiB"
        },
        {
            "label": "vbmeta",
            "size": "1 MiB"
        },
        {
            "label": "storage",
            "grow": "true"
        }
    ]
}
```


## 2019-04-02

To read the CPU temp: https://www.cyberciti.biz/faq/linux-find-out-raspberry-pi-gpu-and-arm-cpu-temperature-command/
```
cat /sys/class/thermal/thermal_zone0/temp
```

CPU frequency:
```
cat /sys/devices/system/cpu/cpu0/cpufreq/cpuinfo_cur_freq
```

### 2019-03-28

Today I learned that all *unstripped* android binaries are stored in the `$ANDROID_PRODUCT_OUT/symbols` dir. The binaries stored here are useful for debugging.

### 2019-03-26

Today I figured out the proper way to set the jenkins user inside a docker image. The Jenkinsfile gets the uid from the locally running node and stores it in `jenkins_user_id`. Then when it goes to build the docker image, it pass the uid as a build arg using `--build-arg JENKINS_UID=+jenkins_user_id`. This will always ensure that the jenkins user on the worker node is the same user in the docker image.

Dockerfile:
```
FROM ubuntu:18.04

ARG JENKINS_UID=99
ARG JENKINS_GID=99
ARG JENKINS_USER=jenkins
ARG JENKINS_GROUP=jenkins

RUN groupadd -g $JENKINS_GID $JENKINS_GROUP
RUN useradd -m -u $JENKINS_UID -g $JENKINS_GROUP $JENKINS_USER

USER $JENKINS_USER

RUN git config --global user.name "Jenkins" && \
    git config --global user.email "admin@videray.com"

```

Jenkinsfile:
```
def jenkins_user_id
def jenkins_group_id

node {
    jenkins_user_id = sh(returnStdout: true, script: 'id -u').trim()
    jenkins_group_id = sh(returnStdout: true, script: 'id -g').trim()
}

pipeline {
    agent {
        dockerfile {
            label 'large && linux'
            additionalBuildArgs '--build-arg JENKINS_UID=' + jenkins_user_id + ' --build-arg JENKINS_GID=' + jenkins_group_id
        }
    }

    stages {
        stage('Build') {
            steps {
                sh 'cd /build && repo sync --force-sync'
                sh 'cd /build && ./aosp_build.sh'
            }
        }
}
```

### 2019-03-22

Today I discovered the android apps that use JNI libraries to link to system libs will get a runtime error. I was running into this problem.

```
java.lang.UnsatisfiedLinkError: dlopen failed: library "libcutils.so"
("/system/lib/libcutils.so") needed or dlopened by "/system/lib/libnativeloader.so"
is not accessible for the namespace "classloader-namespace"
  at java.lang.Runtime.loadLibrary0(Runtime.java:977)
  at java.lang.System.loadLibrary(System.java:1602)
```

With further research, I discovered that this was a [new feature](https://android-developers.googleblog.com/2016/06/improving-stability-with-private-cc.html). Starting in android 7.0, you need to declare [native library namespace](https://source.android.com/devices/tech/config/namespaces_libraries) to allow regular apps to use them. You do this by adding a `/vendor/etc/public.libraries.txt` and listing the names of the public libs within it. *Note*: this rule does not apply to any app signed with the "platform".

