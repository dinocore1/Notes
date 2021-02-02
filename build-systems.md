## CMake

Example project:

```
cmake_minimum_required(VERSION 3.9)
set(CMAKE_CXX_STANDARD 14)
project(evcharge
    VERSION 0.0.1
    LANGUAGES CXX)

find_package(aws-lambda-runtime)
find_package (Eigen3 3.3 REQUIRED NO_MODULE)
find_package(Ceres REQUIRED)

add_executable(evcharge
  src/solar_charger.cc
  src/main.cc
  )

target_include_directories(evcharge PRIVATE src libs/json build/output/include/ceres/internal/miniglog )
target_link_libraries(evcharge PRIVATE Eigen3::Eigen Ceres::ceres)
# target_compile_options(evcharge PRIVATE "-Wall" "-Wextra")

```

### Building

```
mkdir build
cmake -DCMAKE_INSTALL_PREFIX=/path/to/install -DOPTION=ON \
  -S . -B build
cmake --build build -- -j24
cmake --install build
```

## Automake

configure.ac
```
AC_INIT([app],[0.0.1],[paul@sproutlyled.com])
AC_CONFIG_MACRO_DIRS([m4])
AM_INIT_AUTOMAKE([subdir-objects])
AC_PROG_CC
AC_PROG_CXX
AX_CXX_COMPILE_STDCXX([17],,[mandatory])
AC_PROG_RANLIB
AX_PTHREAD(, [AC_MSG_ERROR([libpthread is required])])
PKG_CHECK_MODULES([SDBUSCPP], [sdbus-c++])
PKG_CHECK_MODULES([tiff], [libtiff-4])
AC_CONFIG_FILES([Makefile])
AC_OUTPUT
```

Makefile.am
```
bin_PROGRAMS = sproutly
sproutly_CPPFLAGS =  -I$(top_srcdir)/src \
 -I$(top_srcdir)/src/spdlog \
 -I$(top_srcdir)/src/mqtt-c/include \
 -I$(top_srcdir)/src/cimg \
 -I$(top_srcdir)/src/msgpack \
 $(tiff_CFLAGS) \
 $(SDBUSCPP_CFLAGS)

sproutly_LDADD = $(PTHREAD_CFLAGS) $(tiff_LIBS) $(SDBUSCPP_LIBS) -lPocoFoundation -lPocoJSON -lPocoJWT
sproutly_SOURCES = src/main.cc \
 src/ble-server.cc \
 src/codec.cc \
 src/mqtt-c/src/mqtt.c \
 src/mqtt-c/src/mqtt_pal.c \
 src/mqtt-client.cc \
 src/machine-id.cc \
 src/eventqueue.cc \
 src/file.cc \
 src/wifi-config.cc \
 src/stream.cc \
 src/process.cc \
 src/linux_sensor.cc \
 src/led_device.cc  \
 src/camera.cc \
 src/cameraio.cc

TESTS = test
check_PROGRAMS = test
test_CPPFLAGS = -I$(top_srcdir)/src \
 -I$(top_srcdir)/src/cimg \
 -I$(top_srcdir)/src/gtest \
 -I$(top_srcdir)/src/gtest/include \
 $(tiff_CFLAGS)

test_LDADD = $(PTHREAD_CFLAGS) $(tiff_LIBS)
test_SOURCES = src/gtest/src/gtest-all.cc \
 src/gtest/src/gtest_main.cc \
 src/stream.cc \
 src/codec.cc \
 src/test/test_file.cc \
 src/test/test_stats.cc \
 src/test/test_image.cc \
 src/test/test_schedule.cc \
 src/test/test_led_device.cc \
 src/test/test_process.cc \
 src/test/test_camera.cc \
 src/test/test_cameraio.cc 

FORMAT_FILES = \
	src/*.h \
	src/*.cc

format:
	astyle -n --options=astyle.cfg $(FORMAT_FILES)

```

### Building

```
$ autoreconf -i
$ CPPFLAGS="-g -O0" ./configure
$ make -j24
```