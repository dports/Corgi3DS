cmake_minimum_required(VERSION 3.13)

include(CheckSymbolExists)
include(CheckLibraryExists)

cmake_policy(VERSION 3.13)
if (POLICY CMP0076)
    cmake_policy(SET CMP0076 NEW)
endif()

set(CMAKE_OSX_DEPLOYMENT_TARGET "10.14" CACHE STRING "Minimum OS X deployment version")

set(PROJECT Corgi3DS)
project(${PROJECT} CXX)

set(CMAKE_C_STANDARD 11)
set(CMAKE_CXX_STANDARD 14) # 17
set(CMAKE_RUNTIME_OUTPUT_DIRECTORY ${CMAKE_BINARY_DIR})

set(CMAKE_AUTOMOC ON)
set(CMAKE_AUTORCC ON)
set(CMAKE_AUTOUIC ON)

add_compile_options(-Wall -Wextra)
set(CMAKE_CXX_FLAGS_RELEASE "-O2 -DNDEBUG")

# find Qt
find_package(Qt5 REQUIRED COMPONENTS Core Gui Multimedia Widgets )

check_library_exists(m pow "" LIBM)
if(LIBM)
    link_libraries(m)
endif()

if (NOT CMAKE_BUILD_TYPE)
    set(CMAKE_BUILD_TYPE Release)
endif()

function(detect_architecture symbol arch)
    if (NOT DEFINED ARCHITECTURE)
        set(CMAKE_REQUIRED_QUIET 1)
        check_symbol_exists("${symbol}" "" ARCHITECTURE_${arch})
        unset(CMAKE_REQUIRED_QUIET)

        # The output variable needs to be unique across invocations otherwise
        # CMake's crazy scope rules will keep it defined
        if (ARCHITECTURE_${arch})
            set(ARCHITECTURE "${arch}" PARENT_SCOPE)
            set(ARCHITECTURE_${arch} 1 PARENT_SCOPE)
            add_definitions(-DARCHITECTURE_${arch}=1)
        endif()
    endif()
endfunction()

detect_architecture("__x86_64__" x86_64)
detect_architecture("__i386__" x86)
detect_architecture("__arm__" ARM)
detect_architecture("__aarch64__" ARM64)

if (ARCHITECTURE STREQUAL x86_64 OR ARCHITECTURE STREQUAL ARM64)
	option(ENABLE_JIT "Enable x64 JIT recompiler" ON)
endif()

if (ENABLE_JIT)
	add_definitions(-DJIT_ENABLED)
endif()

if (CMAKE_BUILD_TYPE STREQUAL Release)
	option(ENABLE_LTO "Enable link-time optimization" ON)
else()
	option(ENABLE_LTO "Enable link-time optimization" OFF)
endif()

option(ENABLE_OGLRENDERER "Enable OpenGL renderer" ON)

if (ENABLE_OGLRENDERER)
    add_definitions(-DOGLRENDERER_ENABLED)
endif()

if (CMAKE_BUILD_TYPE STREQUAL Debug)
	add_compile_options(-Og)
endif()

if (CMAKE_BUILD_TYPE STREQUAL Release)
	add_compile_options(-O3)
	add_link_options(-s)
endif()

if (WIN32)
	option(BUILD_STATIC "Statically link dependencies" OFF)
endif()

if (BUILD_STATIC AND WIN32)
    set(CMAKE_FIND_LIBRARY_SUFFIXES .a)
endif()

if (ENABLE_LTO)
	if (WIN32)
		add_compile_options(-flto)
		add_link_options(-flto)
	else()
		add_compile_options(-flto -fPIC)
		add_link_options(-flto -fuse-linker-plugin -pie)
	endif()
	if (CMAKE_CXX_COMPILER_ID STREQUAL "GNU")
		set(CMAKE_AR "gcc-ar")
		set(CMAKE_RANLIB "gcc-ranlib")
	elseif (CMAKE_CXX_COMPILER_ID STREQUAL "Clang")
		find_program(LLD NAMES ld.lld ld64.lld lld-link)
		if (NOT LLD STREQUAL "LLD-NOTFOUND")
			add_link_options(-fuse-ld=lld)
		endif()
		set(CMAKE_AR "llvm-ar")
		set(CMAKE_RANLIB "llvm-ranlib")
	endif()
endif()

option(BUILD_QT_SDL "Build Qt/SDL frontend" ON)

add_subdirectory(src)

if (BUILD_QT_SDL)
	add_subdirectory(src/frontend/qt_sdl)
endif()

set(SOURCES
    src/qt/main.cpp
    src/core/emulator.cpp
    src/core/cpu/arm.cpp
    src/core/cpu/arm_interpret.cpp
    src/core/cpu/arm_disasm.cpp
    src/core/cpu/cp15.cpp
    src/core/cpu/thumb_disasm.cpp
    src/core/cpu/thumb_interpret.cpp
    src/core/arm9/rsa.cpp
    src/core/timers.cpp
    src/core/arm9/dma9.cpp
    src/core/pxi.cpp
    src/core/arm11/mpcore_pmr.cpp
    src/core/arm11/gpu.cpp
    src/core/arm9/aes.cpp
    src/core/arm9/sha.cpp
    src/core/common/bswp.cpp
    src/core/common/rotr.cpp
    src/core/arm9/aes_lib.c
    src/core/arm9/emmc.cpp
    src/core/arm9/interrupt9.cpp
    src/qt/emuwindow.cpp
    src/qt/emuthread.cpp
    src/qt/settings.cpp
    src/qt/settingswindow.cpp
    src/core/i2c.cpp
    src/core/common/exceptions.cpp
    src/core/cpu/mmu.cpp
    src/core/scheduler.cpp
    src/core/cpu/vfp.cpp
    src/core/cpu/vfp_disasm.cpp
    src/core/cpu/vfp_interpreter.cpp
    src/core/sha_engine.cpp
    src/core/arm11/hash.cpp
    src/core/p9_hle.cpp
    src/core/arm11/dsp.cpp
    src/core/arm9/cartridge.cpp
    src/core/arm11/dsp_interpreter.cpp
    src/core/corelink_dma.cpp
    src/core/arm11/wifi.cpp
    src/core/arm11/xtensa.cpp
    src/core/arm11/xtensa_interpreter.cpp
    src/core/arm11/wifi_timers.cpp
    src/core/spi.cpp
)

set(HEADERS
    src/core/emulator.hpp
    src/core/cpu/arm.hpp
    src/core/cpu/arm_disasm.hpp
    src/core/cpu/arm_interpret.hpp
    src/core/common/rotr.hpp
    src/core/cpu/cp15.hpp
    src/core/arm9/rsa.hpp
    src/core/timers.hpp
    src/core/arm9/dma9.hpp
    src/core/pxi.hpp
    src/core/arm11/mpcore_pmr.hpp
    src/core/arm11/gpu.hpp
    src/core/arm9/aes.hpp
    src/core/arm9/sha.hpp
    src/core/common/bswp.hpp
    src/core/arm9/aes_lib.hpp
    src/core/arm9/aes_lib.h
    src/core/arm9/emmc.hpp
    src/core/arm9/interrupt9.hpp
    src/core/i2c.hpp
    src/core/common/common.hpp
    src/core/common/exceptions.hpp
    src/core/cpu/mmu.hpp
    src/core/scheduler.hpp
    src/core/cpu/vfp.hpp
    src/core/sha_engine.hpp
    src/core/arm11/hash.hpp
    src/core/p9_hle.hpp
    src/core/arm11/dsp.hpp
    src/core/arm9/cartridge.hpp
    src/core/arm11/dsp_interpreter.hpp
    src/core/arm11/dsp_reg.hpp
    src/core/arm11/gpu_floats.hpp
    src/core/arm11/vector_math.hpp
    src/core/arm11/signextend.hpp
    src/core/corelink_dma.hpp
    src/core/arm11/wifi.hpp
    src/core/arm11/xtensa.hpp
    src/core/arm11/xtensa_interpreter.hpp
    src/core/arm11/wifi_timers.hpp
    src/core/spi.hpp
    src/qt/emuthread.hpp
    src/qt/settings.hpp
    src/qt/settingswindow.hpp
)

add_executable(${PROJECT} ${SOURCES} ${HEADERS} ${MOC})
target_link_libraries(${PROJECT} Qt5::Core Qt5::Gui Qt5::Multimedia Qt5::Widgets gmpxx gmp )