---
title: "Using SDL2 with vcpkg and CMake"
date: 2020-09-04
draft: false
toc: false
images:
tags:
  - vcpkg
  - cmake
  - c++
  - sdl
---

Managing dependencies in C++ is much more complicated than it needs to be, thankfully there is a tool from Microsoft that makes it super easy!

[vcpkg]: https://github.com/Microsoft/vcpkg

is a pseudo-Maven for C++ projects which helps managing libraries; it even manages artifacts for different architectures, very neat :)

After integrating with your build system, just download SDL.

```powershell
.\vcpkg\vcpkg.exe install sdl2:x64-windows
```

Be careful about selecting the correct triplet (x64-windows in my example), otherwise your linking will fail!

If integrated with VC++, this should already leave SDL ready to be used with #include, no more messing around with C++ Linker options, VC++ directories and all that nonsense!

If you want to use CLion, however, you'll need to mess around with the CMake file, just add the adequate flag to your CMake configuration:

```
-DCMAKE_TOOLCHAIN_FILE=C:/dev/vcpkg/scripts/buildsystems/vcpkg.cmake
```

(this is all further explained in vcpkg when you execute it!)

And finally the CMake file.

```cmake
cmake_minimum_required(VERSION 3.17)

project(my_project)

set(CMAKE_CXX_STANDARD 17)

find_package(SDL2 CONFIG REQUIRED)
find_package(sdl2-image CONFIG REQUIRED)
find_package(sdl2-ttf CONFIG REQUIRED)

add_executable(my_project main.cpp)

target_link_libraries(my_project PRIVATE SDL2::SDL2 SDL2::SDL2main)
target_link_libraries(my_project PRIVATE SDL2::SDL2_image)
target_link_libraries(my_project PRIVATE SDL2::SDL2_ttf)
```

vcpkg is nice enough to tell you exactly which lines to copy and paste in your CMake file.