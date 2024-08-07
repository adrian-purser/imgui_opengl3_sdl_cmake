cmake_minimum_required(VERSION 3.24)
project(imgui_opengl3_sdl)

cmake_policy(SET CMP0072 NEW)
#cmake_policy(SET CMP0135 NEW)

include(FetchContent)
set(FETCHCONTENT_QUIET FALSE)

set(OpenGL_GL_PREFERENCE GLVND)
set(CMAKE_CXX_STANDARD 20)
set(CMAKE_CXX_STANDARD_REQUIRED)

#find_package(glfw3 REQUIRED)
find_package(OpenGL REQUIRED)

if("${CMAKE_SYSTEM_NAME}" STREQUAL "Darwin")
    set(OS_MACOSX 1)
    set(OS_POSIX 1)
elseif("${CMAKE_SYSTEM_NAME}" STREQUAL "Linux")
    set(OS_LINUX 1)
    set(OS_POSIX 1)
elseif("${CMAKE_SYSTEM_NAME}" STREQUAL "Windows")
    set(OS_WINDOWS 1)
else()
    message(FATAL_ERROR "Unsupported target platform")
endif()

#==============================================================================
#	SDL
#==============================================================================

message(STATUS "Adding SDL...")

set( SDL3_DISABLE_SDL3MAIN ON CACHE INTERNAL "Disable SDL3Main")
set( SDL3_DISABLE_INSTALL ON CACHE BOOL "" FORCE)
set( SDL3_DISABLE_UNINSTALL ON CACHE BOOL "" FORCE)
set( SDL_STATIC ON CACHE BOOL "" FORCE )
set( SDL_SHARED OFF CACHE BOOL "" FORCE )
set( SDL_TEST_ENABLED_BY_DEFAULT OFF CACHE BOOL "" FORCE )
set( SDL_TEST_LIBRARY OFF CACHE BOOL "" FORCE )

FetchContent_Declare(
    SDL
    GIT_REPOSITORY  https://github.com/libsdl-org/SDL
    GIT_TAG         main
    GIT_SHALLOW     TRUE
#    GIT_PROGRESS    TRUE
)

FetchContent_MakeAvailable(SDL)

message(STATUS "SDL Include Dir: ${SDL_INCLUDE_DIRS}")
message(STATUS "SDL Source Dir: ${SDL_SOURCE_DIR}")
message(STATUS "SDL Binary Dir: ${SDL_BINARY_DIR}")

#==============================================================================
#	IMGUI
#==============================================================================

message(STATUS "Adding ImGui...")

set( IMGUI_CMAKE_GIT_REPO "https://github.com/ocornut/imgui.git" CACHE STRING "ImGui Git Repository")
set( IMGUI_CMAKE_GIT_TAG "master" CACHE STRING "ImGui Git Branch/Tag")

message(STATUS "ImGui Git Repository: ${IMGUI_CMAKE_GIT_REPO}")
message(STATUS "ImGui Git Branch/Tag: ${IMGUI_CMAKE_GIT_TAG}")

FetchContent_Declare(
    imgui
    GIT_REPOSITORY  ${IMGUI_CMAKE_GIT_REPO} 
    GIT_TAG         origin/${IMGUI_CMAKE_GIT_TAG}
    GIT_SHALLOW     TRUE
#    GIT_PROGRESS    TRUE
)

FetchContent_MakeAvailable(imgui)

message(STATUS "ImGui Source Dir: ${imgui_SOURCE_DIR}")
message(STATUS "ImGui Binary Dir: ${imgui_BINARY_DIR}")

set(SOURCES
	${imgui_SOURCE_DIR}/imconfig.h
	${imgui_SOURCE_DIR}/imgui.cpp
	${imgui_SOURCE_DIR}/imgui_demo.cpp
	${imgui_SOURCE_DIR}/imgui_draw.cpp
	${imgui_SOURCE_DIR}/imgui_internal.h
	${imgui_SOURCE_DIR}/imgui_tables.cpp
	${imgui_SOURCE_DIR}/imgui_widgets.cpp
	${imgui_SOURCE_DIR}/imstb_rectpack.h
	${imgui_SOURCE_DIR}/imstb_textedit.h
	${imgui_SOURCE_DIR}/imstb_truetype.h	
	${imgui_SOURCE_DIR}/backends/imgui_impl_opengl3.cpp
	${imgui_SOURCE_DIR}/backends/imgui_impl_opengl3.h
	${imgui_SOURCE_DIR}/backends/imgui_impl_sdl3.cpp
	${imgui_SOURCE_DIR}/backends/imgui_impl_sdl3.h
	${imgui_SOURCE_DIR}/misc/cpp/imgui_stdlib.h
	${imgui_SOURCE_DIR}/misc/cpp/imgui_stdlib.cpp
)

add_library(imgui_opengl3_sdl STATIC ${SOURCES})

#==============================================================================
# PLATFORM LIBRARIES
#==============================================================================

if(OS_POSIX)
	list(APPEND LIBS GL)	
endif()

if(OS_WINDOWS)
	list(APPEND LIBS gdi32 opengl32 imm32)	
endif()

target_compile_features(imgui_opengl3_sdl PUBLIC cxx_std_20)
target_include_directories(imgui_opengl3_sdl PUBLIC ${imgui_SOURCE_DIR})
target_link_libraries(imgui_opengl3_sdl PUBLIC SDL3::SDL3-static ${LIBS})



