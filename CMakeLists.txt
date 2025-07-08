cmake_minimum_required (VERSION 3.15)

project ("STM32F103"  C CXX ASM)
set(CMAKE_SYSTEM_NAME Linux)
set(CMAKE_SYSTEM_PROCESSOR arm)

set(CMAKE_MAKE_PROGRAM make)
set(CMAKE_C_COMPILER arm-none-eabi-gcc)
set(CMAKE_CXX_COMPILER arm-none-eabi-g++)
set(CMAKE_ASM_COMPILER arm-none-eabi-gcc)
set(CMAKE_AR /arm-none-eabi-ar)
set(CMAKE_OBJCOPY arm-none-eabi-objcopy)
set(CMAKE_OBJDUMP arm-none-eabi-objdump)
set(SIZE arm-none-eabi-size)
set(CMAKE_TRY_COMPILE_TARGET_TYPE STATIC_LIBRARY)
set(CMAKE_FIND_ROOT_PATH_MODE_PROGRAM NEVER)
set(CMAKE_FIND_ROOT_PATH_MODE_LIBRARY ONLY)
set(CMAKE_FIND_ROOT_PATH_MODE_INCLUDE ONLY)
set(CMAKE_FIND_ROOT_PATH_MODE_PACKAGE ONLY)

#Uncomment for hardware floating point
#add_compile_definitions(ARM_MATH_CM4;ARM_MATH_MATRIX_CHECK;ARM_MATH_ROUNDING)
#add_compile_options(-mfloat-abi=hard -mfpu=fpv4-sp-d16)
#add_link_options(-mfloat-abi=hard -mfpu=fpv4-sp-d16)
 
#Uncomment for software floating point
#add_compile_options(-mfloat-abi=soft)
 
add_compile_options(-mcpu=cortex-m3 -mthumb -mthumb-interwork)
add_compile_options(-ffunction-sections -fdata-sections -fno-common -fmessage-length=0)

#添加宏定义
add_definitions(-DUSE_STDPERIPH_DRIVER -DSTM32F10X_MD)


# 将源代码添加到此项目的可执行文件。
# add_executable (STM32F103 "User/src/main.c")
#添加头文件路径，即.h文件
include_directories(./STM32F10x_FWLib/inc ./User/inc ./Cmsis ./Hardwear/DCMotor ./Hardwear/DHT11 ./Hardwear/Infrared ./Hardwear/KEY ./Hardwear/LED ./Hardwear/OLED ./Hardwear/RELAY ./Hardwear/RGB ./Hardwear/StepMotor ./Hardwear/WiFi)
#添加源文件路径,即.c或者.s文件
file(GLOB_RECURSE SOURCES ./STM32F10x_FWLib/src/*.c ./User/src/*.c ./Hardwear/DCMotor/*.c ./Hardwear/DHT11/*.c ./Hardwear/Infrared/*.c ./Hardwear/KEY/*.c ./Hardwear/LED/*.c ./Hardwear/OLED/*.c ./Hardwear/RELAY/*.c ./Hardwear/RGB/*.c ./Hardwear/StepMotor/*.c ./Hardwear/WiFi/*.c ./Cmsis/*.*)

#添加你的stm32f10x_flash_extsram.ld的连接脚本路径
set(LINKER_SCRIPT ${CMAKE_SOURCE_DIR}/stm32f10x_flash_extsram.ld)
 
add_link_options(-Wl,-gc-sections,--print-memory-usage,-Map=${PROJECT_BINARY_DIR}/${PROJECT_NAME}.map)
#选择cortex-m3内核
add_link_options(-mcpu=cortex-m3 -mthumb -mthumb-interwork)
add_link_options(-T ${LINKER_SCRIPT})
 
add_link_options(-specs=nano.specs -specs=nosys.specs  -u_printf_float)
 
add_executable(${PROJECT_NAME}.elf ${SOURCES} ${LINKER_SCRIPT})
 
set(HEX_FILE ${PROJECT_BINARY_DIR}/${PROJECT_NAME}.hex)
set(BIN_FILE ${PROJECT_BINARY_DIR}/${PROJECT_NAME}.bin)
 
add_custom_command(TARGET ${PROJECT_NAME}.elf POST_BUILD
        COMMAND ${CMAKE_OBJCOPY} -Oihex $<TARGET_FILE:${PROJECT_NAME}.elf> ${HEX_FILE}
        COMMAND ${CMAKE_OBJCOPY} -Obinary $<TARGET_FILE:${PROJECT_NAME}.elf> ${BIN_FILE}
        COMMENT "Building ${HEX_FILE}
Building ${BIN_FILE}")

