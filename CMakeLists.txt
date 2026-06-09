#@author: Deng Zhimao
#@email: dengzhimao@alientek.com
#B站作品: https://space.bilibili.com/474103963?spm_id_from=333.788.0.0

#开源电子网: http://www.openedv.com/forum.php
#正点原子官网: https://www.alientek.com
#店铺: https://zhengdianyuanzi.tmall.com
cmake_minimum_required(VERSION 3.8)
message(STATUS "此程序为瑞芯微编写的电机控制程序，笔者将它作为一个LVGL示例供大家学习，只是一个界面演示，没有实际功能\n-- 编译成功后将当前路径的resource文件夹拷贝到开发板的/usr/share/下")
project(motor_demo)

if (LVGL_V9)
	MESSAGE(FATAL_ERROR "This app no support LVGL9, please switch to LVGL8")
endif()

include_directories(${PROJECT_SOURCE_DIR}/)

link_directories(./)

aux_source_directory(. SRCS)

#add_executable(${PROJECT_NAME} ${SRCS})

#This demo only provides ui display. For details about the prototype library, consult RK
#target_link_libraries(${PROJECT_NAME}
#	madht1505ba1 ethercat
#)

#install(TARGETS ${PROJECT_NAME} RUNTIME DESTINATION bin)
#install(DIRECTORY resource DESTINATION share)

set(TOOLCHAIN_DIR /opt/atk-dlrk3506-toolchain)
set(CMAKE_CXX_COMPILER ${TOOLCHAIN_DIR}/bin/arm-buildroot-linux-gnueabihf-g++)
set(CMAKE_C_COMPILER ${TOOLCHAIN_DIR}/bin/arm-buildroot-linux-gnueabihf-gcc)
set(SYSROOT ${TOOLCHAIN_DIR}/arm-buildroot-linux-gnueabihf/sysroot/usr/include)
set(CMAKE_SYSROOT ${TOOLCHAIN_DIR}/arm-buildroot-linux-gnueabihf/sysroot)


include_directories(
        ${CMAKE_SYSROOT}/usr/include/lvgl
        ${CMAKE_SYSROOT}/usr/include/lvgl/lv_drivers
        ${CMAKE_SYSROOT}/usr/include/c++/12.4.0
        )

add_definitions(-DUSE_RK3506=1)
add_definitions(-DUSE_DRM=1 -DUSE_EVDEV=1)

include_directories(${PROJECT_SOURCE_DIR})
include_directories(${PROJECT_SOURCE_DIR}/lvgl8)
include_directories(${PROJECT_SOURCE_DIR}/common)
include_directories(${PROJECT_SOURCE_DIR}/sys)

aux_source_directory(${PROJECT_SOURCE_DIR}/lvgl8 SRCS)
aux_source_directory(${PROJECT_SOURCE_DIR}/sys SRCS)
aux_source_directory(${PROJECT_SOURCE_DIR}/common SRCS)
aux_source_directory(. SRCS)

add_executable(${PROJECT_NAME} ${SRCS})

target_link_libraries(${PROJECT_NAME}
        lvgl lv_drivers freetype drm evdev m
)

add_custom_target(distclean
    COMMAND ${CMAKE_COMMAND} -E remove CMakeCache.txt
    COMMAND ${CMAKE_COMMAND} -E remove Makefile
    COMMAND ${CMAKE_COMMAND} -E remove_directory CMakeFiles
    COMMAND ${CMAKE_COMMAND} -E remove cmake_install.cmake
    COMMAND ${CMAKE_COMMAND} -E remove ${PROJECT_NAME}
    COMMENT "Clean all CMake generated files and ${PROJECT_NAME}"
)
