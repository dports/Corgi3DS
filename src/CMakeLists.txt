project(core)

set (CMAKE_CXX_STANDARD 17)

add_library(core STATIC
	qt/main.cpp
	core/emulator.cpp
	core/cpu/arm.cpp
	core/cpu/arm_interpret.cpp
	core/cpu/arm_disasm.cpp
	core/cpu/cp15.cpp
	core/cpu/thumb_disasm.cpp
	core/cpu/thumb_interpret.cpp
	core/arm9/rsa.cpp
	core/timers.cpp
	core/arm9/dma9.cpp
	core/pxi.cpp
	core/arm11/mpcore_pmr.cpp
	core/arm11/gpu.cpp
    #src/core/arm9/aes.cpp
	core/arm9/sha.cpp
	core/common/bswp.cpp
	core/common/rotr.cpp
    #src/core/arm9/aes_lib.c
	core/arm9/emmc.cpp
	core/arm9/interrupt9.cpp
	qt/emuwindow.cpp
	qt/emuthread.cpp
	qt/settings.cpp
	qt/settingswindow.cpp
	core/i2c.cpp
	core/common/exceptions.cpp
	core/cpu/mmu.cpp
	core/scheduler.cpp
	core/cpu/vfp.cpp
	core/cpu/vfp_disasm.cpp
	core/cpu/vfp_interpreter.cpp
	core/sha_engine.cpp
	core/arm11/hash.cpp
	core/p9_hle.cpp
	core/arm11/dsp.cpp
	core/arm9/cartridge.cpp
	core/arm11/dsp_interpreter.cpp
	core/corelink_dma.cpp
	core/arm11/wifi.cpp
	core/arm11/xtensa.cpp
	core/arm11/xtensa_interpreter.cpp
	core/arm11/wifi_timers.cpp
	core/spi.cpp
	
	core/emulator.hpp
	core/cpu/arm.hpp
	core/cpu/arm_disasm.hpp
	core/cpu/arm_interpret.hpp
	core/common/rotr.hpp
	core/cpu/cp15.hpp
	core/arm9/rsa.hpp
	core/timers.hpp
	core/arm9/dma9.hpp
	core/pxi.hpp
	core/arm11/mpcore_pmr.hpp
	core/arm11/gpu.hpp
    #src/core/arm9/aes.hpp
	core/arm9/sha.hpp
	core/common/bswp.hpp
    #src/core/arm9/aes_lib.hpp
    #src/core/arm9/aes_lib.h
	core/arm9/emmc.hpp
	core/arm9/interrupt9.hpp
	core/i2c.hpp
	core/common/common.hpp
	core/common/exceptions.hpp
	core/cpu/mmu.hpp
	core/scheduler.hpp
	core/cpu/vfp.hpp
	core/sha_engine.hpp
	core/arm11/hash.hpp
	core/p9_hle.hpp
	core/arm11/dsp.hpp
	core/arm9/cartridge.hpp
	core/arm11/dsp_interpreter.hpp
	core/arm11/dsp_reg.hpp
	core/arm11/gpu_floats.hpp
	core/arm11/vector_math.hpp
	core/arm11/signextend.hpp
	core/corelink_dma.hpp
	core/arm11/wifi.hpp
	core/arm11/xtensa.hpp
	core/arm11/xtensa_interpreter.hpp
	core/arm11/wifi_timers.hpp
	core/spi.hpp
	qt/emuthread.hpp
	qt/settings.hpp
	qt/settingswindow.hpp

	tiny-AES-c/aes.c
)

if (APPLE)
        target_include_directories(core PUBLIC /usr/local/include /opt/homebrew/include)
        target_link_directories(core PUBLIC /usr/local/lib /opt/homebrew/lib)
endif()
