## Build System

The Android build system is a specialized Makefile framework. The framework looks for and parses `Android.mk` files in each project directory.

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