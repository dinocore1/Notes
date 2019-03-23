

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

