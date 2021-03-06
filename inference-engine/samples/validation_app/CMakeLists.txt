# Copyright (C) 2018 Intel Corporation
# SPDX-License-Identifier: Apache-2.0
#

cmake_minimum_required(VERSION 2.8)

set (TARGET_NAME "validation_app")

file (GLOB MAIN_SRC
        ${CMAKE_CURRENT_SOURCE_DIR}/*.cpp
        ${CMAKE_CURRENT_SOURCE_DIR}/pugixml/*.cpp
        )

file (GLOB MAIN_HEADERS
        ${CMAKE_CURRENT_SOURCE_DIR}/*.hpp
        ${CMAKE_CURRENT_SOURCE_DIR}/pugixml/*.hpp
        )

# Create named folders for the sources within the .vcproj
# Empty name lists them directly under the .vcproj
source_group("src" FILES ${MAIN_SRC})
source_group("include" FILES ${MAIN_HEADERS})

# Find OpenCV components if exist
find_package(OpenCV COMPONENTS imgcodecs QUIET)
if(NOT(OpenCV_FOUND))
    message(WARNING "OPENCV is disabled or not found, " ${TARGET_NAME} " skipped")
    return()
endif()

# Properties->C/C++->General->Additional Include Directories
include_directories (${CMAKE_CURRENT_SOURCE_DIR}/../classification_sample/core
        ${CMAKE_CURRENT_SOURCE_DIR}/../common
        ${CMAKE_CURRENT_SOURCE_DIR}/../common/os/windows
        ${CMAKE_CURRENT_SOURCE_DIR}/../../include)

link_directories(${LIB_FOLDER})

# Create library file from sources.
add_executable(${TARGET_NAME} ${MAIN_SRC} ${MAIN_HEADERS})

set_target_properties(${TARGET_NAME} PROPERTIES "CMAKE_CXX_FLAGS" "${CMAKE_CXX_FLAGS} -fPIE" 
COMPILE_PDB_NAME ${TARGET_NAME})
target_link_libraries(${TARGET_NAME} gflags IE::ie_cpu_extension ${InferenceEngine_LIBRARIES} ${OpenCV_LIBRARIES})
if (UNIX)
    target_link_libraries(${TARGET_NAME} dl)
endif()

