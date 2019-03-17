The Android build system is a specialized Makefile framework. The framework looks for and parses `Android.mk` files in each project directory. Before you can build anything, you need to "enter" the build system by running:
```
$ . build/envsetup.sh
$ lunch
```
You can then build any target with `m <build_target_name>`

Some other useful build commands:

 * `m clean-<projectname>` every module implicitly defines a clean target with the clean-<projectname> naming convention
 * `m installclean` clean root, system, vendor, etc images. 
 * `m sdk` create sdk located at out/host/${HOST-ARCH}/sdk/../../  You can then point Android Studio to the SDK location (File -> Project Structure -> SDK Location) Note: to get the sdk taget build to work, you need to have these in your product .mk: $(call inherit-product, development/build/product_sdk.mk) $(call inherit-product, sdk/build/product_sdk.mk) Alternatively, you can "lunch sdk_phone_arm64" and then "m sdk" https://developer.android.com/ndk/guides/android_mk 
 * `m update-api`


### Build Exe

```
include $(CLEAR_VARS) 
LOCAL_MODULE := fwtool 
LOCAL_VENDOR_MODULE := true
LOCAL_CLANG := true 
LOCAL_SRC_FILES := \
  flash_ec.c \
  flash_mtd.c \
  flash_device.c \
  update_fw.cpp 

LOCAL_SHARED_LIBRARIES := liblog 
LOCAL_CFLAGS += -Wno-unused-parameter -DUSE_LOGCAT 
LOCAL_C_INCLUDES += bootable/recovery 
include $(BUILD_EXECUTABLE) 
```

### Shared Lib 

```
include $(CLEAR_VARS) 
LOCAL_VENDOR_MODULE := true 
LOCAL_MODULE_RELATIVE_PATH := hw 
LOCAL_SHARED_LIBRARIES := liblog libcutils 
LOCAL_HEADER_LIBRARIES := libhardware_headers 
LOCAL_C_INCLUDES += $(LOCAL_PATH)/../include 
LOCAL_SRC_FILES := sensors_qemu.c 
LOCAL_MODULE := sensors.ranchu 
include $(BUILD_SHARED_LIBRARY)
```

### Soong
More recently, a new build system [Soong](https://android.googlesource.com/platform/build/soong/) was introduced as an eventual replacement for the Makefiles. As of v.9 (PIE) Soong has not completely replaced Android.mk files yet, but it seems that all new project are encouraged to use it.