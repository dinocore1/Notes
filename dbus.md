

https://www.freedesktop.org/wiki/IntroductionToDBus/

http://www.matthew.ath.cx/misc/dbus

https://github.com/RadiusNetworks/bluez/blob/master/doc/gatt-api.txt

https://github.com/Kistler-Group/sdbus-cpp

https://patchwork.ozlabs.org/project/buildroot/patch/20190807191146.32047-1-jfaith@impinj.com/

http://0pointer.net/blog/the-new-sd-bus-api-of-systemd.html

## Config DBus policies

https://dbus.freedesktop.org/doc/dbus-daemon.1.html

example:
```
<!DOCTYPE busconfig PUBLIC "-//freedesktop//DTD D-BUS Bus Configuration 1.0//EN"
 "http://www.freedesktop.org/standards/dbus/1.0/busconfig.dtd">
<busconfig>
    <policy user="root">
        <allow own="com.sproutly"/>
        <allow send_destination="com.sproutly"/>
        <allow send_interface="com.sproutly.sproutly"/>
    </policy>
    <policy at_console="true">
        <allow send_destination="com.sproutly"/>
    </policy>
    <policy context="default">
        <deny send_destination="com.sproutly"/>
    </policy>
</busconfig>
```



## Reload DBus config

```
systemctl reload dbus.service
```

## View The Objects on the bus

```
busctl tree
```

### Register Obj On Bus

`dbus_connection_register_object_path`