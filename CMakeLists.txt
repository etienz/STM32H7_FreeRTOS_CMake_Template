cmake_minimum_required(VERSION 3.16)
set(CMAKE_TOOLCHAIN_FILE ${CMAKE_CURRENT_SOURCE_DIR}/../../STM32_cmake/stm32-cmake/cmake/stm32_gcc.cmake)

project(stm32-template C ASM)

set(PDM_LIB_PATH "/opt/STM32CubeH7/Middlewares/ST/STM32_Audio/Addons/PDM")

#Find CMSIS for both cores
find_package(CMSIS COMPONENTS STM32H747XI_M4 STM32H747XI_M7 RTOS REQUIRED)
find_package(HAL COMPONENTS STM32H7M4 STM32H7M7 RCC GPIO CORTEX TIM HSEM PWR REQUIRED)
find_package(FreeRTOS COMPONENTS ARM_CM4F ARM_CM7 STM32H7 REQUIRED)
#find_package(BSP COMPONENTS STM32H747I_Discovery STM32H7 REQUIRED)

set(M7_TARGET_NAME m7core)
set(M4_TARGET_NAME m4core)

set(M7_SRC CM7/Src)
set(M4_SRC CM4/Src)
set(COM_SRC Common/Src)



#Creating both binaries
add_executable(${M7_TARGET_NAME})
target_sources(${M7_TARGET_NAME} PRIVATE
        ${M7_SRC}/main.c
        ${M7_SRC}/stm32h7xx_hal_timebase_tim.c
        ${M7_SRC}/stm32h7xx_it.c

        #Common/Src/system_stm32h7xx.c
        Common/Src/stm32h747i_discovery.c
        )
target_include_directories(${M7_TARGET_NAME} PRIVATE
        CM7/Inc

        Common/Inc
        ${PDM_LIB_PATH}/Inc
        )
target_link_libraries(${M7_TARGET_NAME} PRIVATE

    FreeRTOS::STM32::H7::M7::ARM_CM7
    FreeRTOS::STM32::H7::M7::Heap::4

    HAL::STM32::H7::M7::RCC
    HAL::STM32::H7::M7::GPIO
    HAL::STM32::H7::M7::CORTEX
    HAL::STM32::H7::M7::TIM
    HAL::STM32::H7::M7::TIMEx
    HAL::STM32::H7::M7::HSEM
    HAL::STM32::H7::M7::PWR
    HAL::STM32::H7::M7::PWREx

    CMSIS::STM32::H747XI::M7
    CMSIS::STM32::H7::M7::RTOS

    #${PDM_LIB_PATH}/Lib/libPDMFilter_CM7_GCC_wc32.a
    #${PDM_LIB_PATH}/Lib/libPDMFilter_CM7_GCC_wc32_softfp.a

    #BSP::STM32::STM32H747I_Discovery
    STM32::NoSys
    )
stm32_print_size_of_target(${M7_TARGET_NAME})

add_executable(${M4_TARGET_NAME})
target_sources(${M4_TARGET_NAME} PRIVATE
        ${M4_SRC}/main.c
        ${M4_SRC}/stm32h7xx_it.c

        #Common/Src/system_stm32h7xx.c
        Common/Src/stm32h747i_discovery.c
        )
target_include_directories(${M4_TARGET_NAME} PRIVATE
        CM4/Inc

        Common/Inc
        ${PDM_LIB_PATH}/Inc
        )
target_link_libraries(${M4_TARGET_NAME} PRIVATE
    #BSP::STM32::STM32H747I_Discovery

    FreeRTOS::STM32::H7::M4::ARM_CM4F
        FreeRTOS::STM32::H7::M4::Heap::4

    HAL::STM32::H7::M4::RCC
    HAL::STM32::H7::M4::GPIO
    HAL::STM32::H7::M4::CORTEX
    HAL::STM32::H7::M4::TIM
    HAL::STM32::H7::M4::HSEM
    HAL::STM32::H7::M4::PWR
    HAL::STM32::H7::M4::PWREx

    CMSIS::STM32::H747XI::M4
    CMSIS::STM32::H7::M4::RTOS

    #${PDM_LIB_PATH}/Lib/libPDMFilter_CM4_GCC_wc32.a
    #${PDM_LIB_PATH}/Lib/libPDMFilter_CM4_GCC_wc32_softfp.a

    STM32::NoSys
    )
stm32_print_size_of_target(${M4_TARGET_NAME})
