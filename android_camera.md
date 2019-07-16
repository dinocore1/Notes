

adding the `android.hardware.camera.provider@2.4-service` package creates a /vendor/etc/init/android.hardware.camera.provider@2.4-service.rc file that starts the service at startup. The service name is `vendor.camera-provider-2-4`

```
PRODUCT_PACKAGES += camera.videray
PRODUCT_PROPERTY_OVERRIDES += ro.hardware.camera=videray

PRODUCT_PACKAGES += \
    android.hardware.camera.provider@2.4-impl \
    android.hardware.camera.provider@2.4-service \
    camera.device@1.0-impl \
    camera.device@3.2-impl
```