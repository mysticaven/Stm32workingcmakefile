cmake_minimum_required(VERSION 3.20)

# Cross-compile setup
set(CMAKE_SYSTEM_NAME Generic)
set(CMAKE_SYSTEM_PROCESSOR arm)
set(CMAKE_TRY_COMPILE_TARGET_TYPE STATIC_LIBRARY)

project(working C CXX ASM)

# Compiler
set(CMAKE_C_COMPILER  "C:/ST/STM32CubeCLT_1.20.0/GNU-tools-for-STM32/bin/arm-none-eabi-gcc.exe")
set(CMAKE_CXX_COMPILER "C:/ST/STM32CubeCLT_1.20.0/GNU-tools-for-STM32/bin/arm-none-eabi-g++.exe")

# MCU flags (CORRECT VERSION ONLY ONCE)
set(MCU
        -mcpu=cortex-m3
        -mthumb
        -mfloat-abi=soft
)

# Linker script
set(LINKER_SCRIPT ${CMAKE_SOURCE_DIR}/STM32F100XX_FLASH.ld)

# Include folders
include_directories(
        Core/Inc
        Drivers/CMSIS/Include
        Drivers/CMSIS/Device/ST/STM32F1xx/Include
        Drivers/STM32F1xx_HAL_Driver/Inc
        Drivers/STM32F1xx_HAL_Driver/Inc/Legacy
)

# Sources
set(SOURCES
        Core/Src/main.c
        Core/Src/stm32f1xx_it.c
        Core/Src/stm32f1xx_hal_msp.c
        Core/Src/syscalls.c
        Core/Src/sysmem.c
        Core/Src/system_stm32f1xx.c
        startup_stm32f100xb.s

        Drivers/STM32F1xx_HAL_Driver/Src/stm32f1xx_hal.c
        Drivers/STM32F1xx_HAL_Driver/Src/stm32f1xx_hal_rcc.c
        Drivers/STM32F1xx_HAL_Driver/Src/stm32f1xx_hal_rcc_ex.c
        Drivers/STM32F1xx_HAL_Driver/Src/stm32f1xx_hal_cortex.c
        Drivers/STM32F1xx_HAL_Driver/Src/stm32f1xx_hal_gpio.c
        Drivers/STM32F1xx_HAL_Driver/Src/stm32f1xx_hal_gpio_ex.c
        Drivers/STM32F1xx_HAL_Driver/Src/stm32f1xx_hal_dma.c
        Drivers/STM32F1xx_HAL_Driver/Src/stm32f1xx_hal_flash.c
        Drivers/STM32F1xx_HAL_Driver/Src/stm32f1xx_hal_flash_ex.c
        Drivers/STM32F1xx_HAL_Driver/Src/stm32f1xx_hal_pwr.c
        Drivers/STM32F1xx_HAL_Driver/Src/stm32f1xx_hal_exti.c
)

# Create target
add_executable(working ${SOURCES})

# Compile options
target_compile_definitions(working PRIVATE STM32F100xB USE_HAL_DRIVER)
target_compile_options(working PRIVATE ${MCU} -O0 -g -ffunction-sections -fdata-sections -Wall)
target_link_options(working PRIVATE
        ${MCU}
        -T${LINKER_SCRIPT}
        -Wl,-Map=working.map,--gc-sections
)

# Size after build
add_custom_command(TARGET working POST_BUILD
        COMMAND arm-none-eabi-size working
)
