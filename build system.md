The Android build system is a specialized Makefile framework. The framework looks for and parses `Android.mk` files in each project directory. Before you can build anything, you need to "enter" the build system by running:
```
$ . build/envsetup.sh
$ lunch
```
You can then build any target with `m <build_target_name>`. The default build target is `droid` which ultimatly create the final disk images in `out/target/product/<devicename>`.

The final build results are: 
 * `boot.img` contains the linux kernel and init ramdisk
 * `recovery.img`
 * `system.img` mounted `/system` (or `/` when systemasroot)
 * `vendor.img` mounted `/vendor` - kinda like the system.img but for vendor stuff
 * `userdata.img` mounted `/data`
 * `cache.img` usually an empty ext4 image

The build env has a few useful env vars:
 * `ANDROID_PRODUCT_OUT` absolute path to target build dir
 * `ANDROID_HOST_OUT` absolute path to host build dir
 * `PATH` updated with toolchain
 * `TARGET_PRODUCT`
 * `TARGET_BUILD_VARIANT`
 * `TARGET_BUILD_TYPE`


Some other useful build commands:

 * `m clean-<projectname>` every module implicitly defines a clean target with the clean-<projectname> naming convention
 * `m installclean` clean root, system, vendor, etc images. 
 * `m sdk` create sdk located at out/host/${HOST-ARCH}/sdk/../../  You can then point Android Studio to the SDK location (File -> Project Structure -> SDK Location) Note: to get the sdk taget build to work, you need to have these in your product .mk: $(call inherit-product, development/build/product_sdk.mk) $(call inherit-product, sdk/build/product_sdk.mk) Alternatively, you can "lunch sdk_phone_arm64" and then "m sdk" https://developer.android.com/ndk/guides/android_mk 
 * `m update-api`


https://blog.jayway.com/2012/10/24/a-practical-approach-to-the-aosp-build-system/
https://www.oreilly.com/library/view/embedded-android/9781449327958/ch04.html
https://android.googlesource.com/platform/build/soong/+/HEAD/docs/best_practices.md

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
LOCAL_EXPORT_C_INCLUDE_DIRS := $(LOCAL_PATH)/include
LOCAL_SHARED_LIBRARIES := liblog libcutils 
LOCAL_HEADER_LIBRARIES := libhardware_headers 
LOCAL_C_INCLUDES += $(LOCAL_PATH)/private
LOCAL_SRC_FILES := sensors_qemu.c 
LOCAL_MODULE := sensors.ranchu 
include $(BUILD_SHARED_LIBRARY)
```

### Prebuilt Exe
```
LOCAL_PATH := $(call my-dir)
include $(CLEAR_VARS)
LOCAL_MODULE := busybox
LOCAL_MODULE_CLASS := EXECUTABLES
LOCAL_MODULE_TAGS := optional
LOCAL_SRC_FILES := busybox
LOCAL_MODULE_PATH := $(TARGET_RECOVERY_ROOT_OUT)/sbin
include $(BUILD_PREBUILT)
```
This prebuilt example copies the busybox exe (found in the local dir) to the recovery sbin.

### Prebuilt Shared Lib
```
include $(CLEAR_VARS)
LOCAL_MODULE := libhwbinder.vndk-sp
LOCAL_MODULE_CLASS := SHARED_LIBRARIES
LOCAL_PREBUILT_MODULE_FILE := $(TARGET_OUT)/lib64/$1.so
LOCAL_MULTILIB := 64
LOCAL_MODULE_TAGS := optional
LOCAL_INSTALLED_MODULE_STEM := libhwbinder.so
LOCAL_MODULE_SUFFIX := .so
LOCAL_MODULE_RELATIVE_PATH := vndk-sp
include $(BUILD_PREBUILT)
```
### Java lib
```
include $(BUILD_JAVA_LIBRARY)
include $(BUILD_STATIC_JAVA_LIBRARY)
```
Note: the `BUILD_JAVA_LIBRARY` creates a .jar and places it in the $ANDROID_PRODUCT_OUT/system/framework where as the static does not create a shared jar.

### Unit test

```
include $(CLEAR_VARS)
LOCAL_MODULE := illusion_tests
LOCAL_MODULE_TAGS := tests
LOCAL_SHARED_LIBRARIES := \
  liblog \
  libcutils \
  libutils \
  libOpenCL \
  libIllusion

LOCAL_SRC_FILES := \
  test/main.cpp

include $(BUILD_NATIVE_TEST)
```

### Soong
More recently, a new build system [Soong](https://android.googlesource.com/platform/build/soong/) was introduced as an eventual replacement for the Makefiles. As of v.9 (PIE) Soong has not completely replaced Android.mk files yet, but it seems that all new project are encouraged to use it. To bridge the gap, the tool `androidmk` is used to translate Android.mk files to Android.bp files. Try it for yourself:

```
$ androidmk Android.mk
```

### library

```
cc_library {
    name: "libvideray",
    vendor: true,
    export_include_dirs: ["include"],
    srcs: [
        "src/utils.cpp",
        "src/vdefile.cpp"
    ],
    shared_libs: [
        "libcutils",
        "libutils",
        "libz",
        "libpng",
        "liblz4"
    ],
    cflags: ["-g","-O0"]
}
```


### header-only library

```
cc_library_headers {
	name: "libglm",
	vendor_available: true,
	export_include_dirs: ["."]
}

```

Use header-only libraries with: 
```
LOCAL_HEADER_LIBRARIES += \
    libglm
```

### binaries

```
cc_binary {
    name: "gzip",
    srcs: ["src/test/minigzip.c"],
    shared_libs: ["libz"],
    stl: "none",
}
```


### unit tests

```
cc_test {
   name: "videray_tests",
   defaults: ["libvideray_defaults"],
   /* host_supported: true, */
   test_suites: ["device-tests"],
   srcs: [
     "test/main.cpp",
     "test/vdefiletest.cpp"
   ],
   shared_libs: [
        "libvideray"
   ]
}
```

### Apps (Packages)

Apps signed with the "platform" key have access to all system privileges. They can link to system libraries, even ones not in the /vendor/etc/public-libraries.txt

https://source.android.com/devices/tech/config/namespaces_libraries

