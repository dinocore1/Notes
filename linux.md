
## Firmware loading

https://www.kernel.org/doc/html/v4.17/driver-api/firmware/introduction.html


Firmware loading search path can be modified by writing to:
`/sys/module/firmware_class/parameters/path` on Android, it looks like the loading path has been set to `/vendor/firmware`