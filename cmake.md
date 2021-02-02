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

## Building

```
mkdir build
cmake -DCMAKE_INSTALL_PREFIX=/path/to/install -DOPTION=ON \
  -S . -B build
cmake --build build -- -j24
cmake --install build
```