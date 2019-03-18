
Starting with Android 8.0, Google introduced a new Hardware Abstraction Layer (HAL) architecture as part of an internal project code word [Project Treble](https://android-developers.googleblog.com/2017/05/here-comes-treble-modular-base-for.html). A big part of the new architecture is HAL Interface Definition Language ([HIDL](https://source.android.com/devices/architecture/hidl)). HIDL looks and feels very similar to AIDL and serves much the same purpose.

In the follow example, I will demonstrate using HIDL to define a new hardware interface, implementing the interface in an C++ service, and accessing the interface from a C++ client.

 * https://www.youtube.com/watch?v=2XJAdK9XKcQ - Opersis Video Tutorials on Android's project Treble

## Adding a new Hardware Interface

### create .hal files
add new .hal files to /vendor/vendorname/interfaces/interface_name/1.0
```
package vendor.microsoft.hardware.example@1.0;
 
interface IExample {
    hello(int32_t valueIn) generates (int32_t valueRet1, int32_t valueRet2);
};
```

### Autogenerate Makefiles
Create a script to generate Android.mk/Android.bp files.
```
#!/bin/bash 

source $ANDROID_BUILD_TOP/system/tools/hidl/update-makefiles-helper.sh 

do_makefiles_update \ 
"videray.microsoft.hardware:vendor/microsoft/interfaces" 
```
Executing updatemakefiles.sh will autogenerate Android.mk files that provide the following build targets:

 * `vendor.microsoft.hardware.example-V1.0-java`
 * `vendor.microsoft.hardware.example-V1.0-java-static`
 * `vendor.microsoft.hardware.example@1.0`
 * `vendor.microsoft.hardware.example@1.0_vendor`

Note: the *-java targets create a .jar and place it in ${PRODUCT_OUT}/system/framework/.jar whereas the *-java-static do not create a jar, but can be used to statically include the code.

### Create Default Implementation

Create a implementation of the new hardware interface by running (FROM THE PROJECT ROOT):

```
PACKAGE=vendor.microsoft.hardware.example@1.0 
LOC=device/VENDOR/DEVICENAME/hardware/example/1.0/ 
m -j hidl-gen 
hidl-gen -o $LOC -Lc++-impl -randroid.hardware:hardware/interfaces \ 
    -randroid.hidl:system/libhidl/transport -rvendor.microsoft.hardware:vendor/microsoft/hardware/interfaces $PACKAGE
```

