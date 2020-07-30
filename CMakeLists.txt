cmake_minimum_required(VERSION 3.10)
project(gba_template)

set(DEVKITPRO "/opt/devkitpro")
set(DEVKITARM "${DEVKITPRO}/devkitARM")

if(WIN32)
    set(CMAKE_C_COMPILER "${DEVKITARM}/bin/arm-none-eabi-gcc.exe")
    set(CMAKE_CXX_COMPILER "${DEVKITARM}/bin/arm-none-eabi-g++.exe")
    set(CMAKE_AR "${DEVKITARM}/bin/arm-none-eabi-gcc-ar.exe" CACHE STRING "")
    set(CMAKE_RANLIB "${DEVKITARM}/bin/arm-none-eabi-gcc-ranlib.exe" CACHE STRING "")
else()
    set(CMAKE_C_COMPILER "${DEVKITARM}/bin/arm-none-eabi-gcc")
    set(CMAKE_CXX_COMPILER "${DEVKITARM}/bin/arm-none-eabi-g++")
    set(CMAKE_AR "${DEVKITARM}/bin/arm-none-eabi-gcc-ar" CACHE STRING "")
    set(CMAKE_RANLIB "${DEVKITARM}/bin/arm-none-eabi-gcc-ranlib" CACHE STRING "")
endif()

set(ARCH " -mthumb -mthumb-interwork")
set(CMAKE_C_FLAGS "${CMAKE_C_FLAGS} -g -Wall -O2 -mcpu=arm7tdmi -mtune=arm7tdmi ${ARCH}" CACHE STRING "C flags")
set(CMAKE_CXX_FLAGS "${CMAKE_C_FLAGS} -fno-rtti -fno-exceptions" CACHE STRING "C++ flags")
set(CMAKE_EXE_LINKER_FLAGS "${CMAKE_EXE_LINKER_FLAGS} -specs=gba.specs")

include_directories(${DEVKITPRO}/libgba/include)
link_directories(${DEVKITPRO}/libgba/lib)

include_directories(include)

add_executable(${PROJECT_NAME}.elf src/main.c)
target_link_libraries(${PROJECT_NAME}.elf mm gba)

add_custom_target(${PROJECT_NAME}.gba
    BYPRODUCTS ${PROJECT_NAME}.gba
    COMMAND ${DEVKITARM}/bin/arm-none-eabi-objcopy -O binary $<TARGET_FILE:${PROJECT_NAME}.elf> ${PROJECT_NAME}.gba && ${DEVKITPRO}/tools/bin/gbafix ${PROJECT_NAME}.gba
    DEPENDS ${PROJECT_NAME}.elf
)
