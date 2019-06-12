---
layout: post
title: Setting up SDL
date: 2019-06-12
categories: SDL C++
---

[SDL (Simple DirectMedia Layer)](https://www.libsdl.org/) is an open source library used to manage and simplify interactions with the computer's multimedia hardware components, for example, diplaying images on the monitor screen, receiving input from keyboard and mouse, and playing sounds on the speaker. It's commonly used to make games, so much so that allegedly [it is often misconceived as a game engine](https://en.wikipedia.org/wiki/Simple_DirectMedia_Layer). I've been using, for my [pong](https://github.com/liamst19/pong-study) and [asteroids](https://github.com/liamst19/asteroids-study) projects.

## Setting up SDL in MSYS2

### Install SDL and other required packages in MSYS2:

An installation of [MSYS2](https://www.msys2.org/) is required. The following will install SDL packages:

```
$ pacman --noconfirm --needed -Sy pkg-config make mingw-w64-x86_64-toolchain mingw-w64-x86_64-cmake mingw-w64-x86_64-zlib mingw-w64-x86_64-SDL2 mingw-w64-x86_64-SDL2_image mingw-w64-x86_64-SDL_ttf
```

The `mingw-w64-x86_64-SDL2_image` library enables working with bitmap images, and `mingw-w64-x86_64-SDL_ttf` enables working with text.
   
### CMakeLists.txt

Adding the following into `CMakeLists.txt` will link SDL library to your project:

```
cmake_minimum_required(VERSION 3.7)

project(my_project)

find_package(SDL2 REQUIRED)
include_directories(${SDL2_INCLUDE_DIRS})

add_executable(my_project src/main.cpp)
target_link_libraries(my_project ${SDL2_LIBRARIES})

```

### C++ code

The basic procedure for opening and rendering images onto a window seems to be:

1. Initialize (`SDL_Init()`).
2. Create Window (`SDL_CreateWindow()`).
3. Get pointer to window surface (`SDL_GetWindowSurface()`).
4. Render to surface (e.g., `SDL_FillRect()`).
5. Update window surface (`SDL_UpdateWindowSurface()`).
6. Upon exiting program, free resource (`SDL_DestroyWindow()`) and quit SDL (`SDL_Quit()`).

The following is taken from [LazyFoo's tutorial](https://lazyfoo.net/tutorials/SDL/01_hello_SDL/index2.php). If successfully compiled, the program should open a new window for three seconds.

```cpp
// main.cpp

#include <stdio.h>
#include <SDL2/SDL.h>

int main(int argc, char *argv[]){

  // Window dimensions
  const int WIN_WIDTH{640};
  const int WIN_HEIGHT{480};

  // Pointer to the SDL window
  SDL_Window* window{nullptr};

  //The surface contained by the window 
  SDL_Surface* screen_surface{nullptr};
  SDL_Log("Starting Program");

  //Initialize SDL
  if(SDL_Init(SDL_INIT_VIDEO) < 0){

    // Initialize failed, something went wrong
    printf( "Could not initialize; SDL_Error: %s\n", SDL_GetError() );

  }
  else {

    //Create window
    window = SDL_CreateWindow("SDL Tutorial",
                              SDL_WINDOWPOS_UNDEFINED,
                              SDL_WINDOWPOS_UNDEFINED,
                              SCREEN_WIDTH,
                              SCREEN_HEIGHT,
                              SDL_WINDOW_SHOWN);
    // Check if window was created
    if(window == nullptr){
      printf("Window could not be created; SDL_Error: %s\n", SDL_GetError());
    }
    else {

      //Get window surface
      screen_surface = SDL_GetWindowSurface(window);

      //Fill the surface white
      SDL_FillRect(screen_surface,
                   NULL,
                   SDL_MapRGB(screen_surface->format, 0xFF, 0xFF, 0xFF ));

      //Update the surface
      SDL_UpdateWindowSurface(window);

      //Wait two seconds
      SDL_Delay(3000);
    }
  }

  //Free window resources
  SDL_DestroyWindow(window);

  //Quit SDL subsystems
  SDL_Quit();

  return 0;
}
```

More realistically, you would probably be creating a class with separate methods for initializing, creating, rendering, destroying, etc.
