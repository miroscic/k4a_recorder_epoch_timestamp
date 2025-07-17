# Copyright (c) Microsoft Corporation. All rights reserved.
# Licensed under the MIT License.

cmake_minimum_required(VERSION 3.16)

project(k4arecorder)

add_executable(k4arecorder 
    src/main.cpp 
    src/recorder.cpp 
    ${CMAKE_CURRENT_BINARY_DIR}/version.rc
)

set( CMAKE_MODULE_PATH "${CMAKE_CURRENT_SOURCE_DIR}" ${CMAKE_MODULE_PATH} )

find_package(k4a)
find_package(k4arecord)

target_link_libraries(k4arecorder PRIVATE
    k4a::k4a
    k4a::k4arecord
    )

target_include_directories(k4arecorder PRIVATE src/include)    

# Include ${CMAKE_CURRENT_BINARY_DIR}/version.rc in the target's sources
# to embed version information
set(K4A_FILEDESCRIPTION "Azure Kinect Recording Tool")
set(K4A_ORIGINALFILENAME "k4arecorder.exe")
configure_file(
    ${CMAKE_CURRENT_SOURCE_DIR}/version.rc.in
    ${CMAKE_CURRENT_BINARY_DIR}/version.rc
    @ONLY
    )

# Setup install
include(GNUInstallDirs)

install(
    TARGETS
        k4arecorder
    RUNTIME DESTINATION
        ${CMAKE_INSTALL_BINDIR}
    COMPONENT
        tools
)

if ("${CMAKE_SYSTEM_NAME}" STREQUAL "Windows")
    install(
        FILES
            $<TARGET_PDB_FILE:k4arecorder>
        DESTINATION
            ${CMAKE_INSTALL_BINDIR}
        COMPONENT
            tools
        OPTIONAL
    )
endif()