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

note: in my example i also linked libraries to sdl2-image and sdl2-ttf library, previously installed using vcpkg.

You're all set to use SDL2 in your C++ project.

Following is a quick example taken from the wonderful [LazyFoo's SDL2 tutorial](https://lazyfoo.net/tutorials/SDL/index.php):

```c++
#include <iostream>
#include <SDL_image.h>
#include <SDL.h>

SDL_Window *gWindow = NULL;
SDL_Renderer *gRenderer = NULL;

bool init() {
    //Initialization flag
    bool success = true;

    //Initialize SDL
    if (SDL_Init(SDL_INIT_VIDEO) < 0) {
        printf("SDL could not initialize! SDL Error: %s\n", SDL_GetError());
        success = false;
    } else {
        //Set texture filtering to linear
        if (!SDL_SetHint(SDL_HINT_RENDER_SCALE_QUALITY, 0)) {
            printf("Warning: Linear texture filtering not enabled!");
        }

        //Create window
        gWindow = SDL_CreateWindow("My first window", SDL_WINDOWPOS_UNDEFINED,
                                   SDL_WINDOWPOS_UNDEFINED, 640, 480, SDL_WINDOW_SHOWN);
        if (gWindow == NULL) {
            printf("Window could not be created! SDL Error: %s\n", SDL_GetError());
            success = false;
        } else {
            //Create renderer for window
            gRenderer = SDL_CreateRenderer(gWindow, -1, SDL_RENDERER_ACCELERATED | SDL_RENDERER_PRESENTVSYNC);
            if (gRenderer == NULL) {
                printf("Renderer could not be created! SDL Error: %s\n", SDL_GetError());
                success = false;
            } else {
                //Initialize renderer color
                SDL_SetRenderDrawColor(gRenderer, 0xFF, 0xFF, 0xFF, 0xFF);

                //Initialize PNG loading
                int imgFlags = IMG_INIT_PNG;
                if (!(IMG_Init(imgFlags) & imgFlags)) {
                    printf("SDL_image could not initialize! SDL_image Error: %s\n", IMG_GetError());
                    success = false;
                }
            }
        }
    }

    return success;
}

int main(int argc, char *args[]) {
    std::cout << "Hello, World!" << std::endl;

    //Start up SDL and create window
    if (!init()) {
        printf("Failed to initialize!\n");
    } else {
        //Load media

        //Main loop flag
        bool quit = false;

        //Event handler
        SDL_Event e;

        SDL_RenderSetLogicalSize(gRenderer, 320, 200);

        //While application is running
        while (!quit) {
            //Handle events on queue
            while (SDL_PollEvent(&e) != 0) {
                //User requests quit
                if (e.type == SDL_QUIT) {
                    quit = true;
                }
            }

            //Clear screen
            SDL_SetRenderDrawColor(gRenderer, 0, 0, 0xAF, 0);
            SDL_RenderClear(gRenderer);

            //Update screen
            SDL_RenderPresent(gRenderer);
        }
    }

    return 0;
}
```