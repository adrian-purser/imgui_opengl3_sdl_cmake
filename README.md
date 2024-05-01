# imgui_opengl3_sdl_cmake
CMake wrapper to allow imgui to be used with FetchContent


Usage
-----

```
FetchContent_Declare(
	imgui_opengl3_sdl
	GIT_REPOSITORY https://github.com/adrian-purser/imgui_opengl3_sdl_cmake.git
	GIT_TAG origin/docking
)
FetchContent_MakeAvailable(imgui_opengl3_sdl)

target_link_libraries(${CMAKE_PROJECT_NAME} PUBLIC imgui_opengl3_sdl)
```

