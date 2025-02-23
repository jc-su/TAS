cmake_minimum_required(VERSION 3.21)
project(TAS LANGUAGES CUDA CXX)
find_package(CUDA)
include(FindCUDA)
set(CMAKE_CUDA_STANDARD 20)

list(APPEND CUDA_NVCC_FLAGS
        "-arch=sm_61;
        -std=c++11;
        -cudart=shared;"
        )

if (CMAKE_BUILD_TYPE STREQUAL "Debug")
    set(CMAKE_CXX_FLAGS_DEBUG "${CMAKE_CXX_FLAGS} -O0 -g -DDEBUG")
    list(APPEND CUDA_NVCC_FLAGS "-g -O0")

else ()
    set(CMAKE_BUILD_TYPE "Release")
    set(CMAKE_CXX_FLAGS_RELEASE "${CMAKE_CXX_FLAGS} -O3 -Wall")
    list(APPEND CUDA_NVCC_FLAGS "-O3;")
endif (CMAKE_BUILD_TYPE STREQUAL "Debug")


set(CUDA_PROPAGATE_HOST_FLAGS OFF)


set(SOURCE_DIR src)
set(INCLUDE_DIR include)

file(GLOB SOURCES_LIB
        "${SOURCE_DIR}/*.cu"
        "${SOURCE_DIR}/*.cpp"
        "${SOURCE_DIR}/*.c"
        )
list(REMOVE_ITEM SOURCES_LIB
        ${SOURCE_DIR}/main.cu
        )

file(GLOB INCLUDE
        "${INCLUDE_DIR}/*.cuh"
        "${INCLUDE_DIR}/*.h"
        "${INCLUDE_DIR}/*.hpp"
        )

include_directories(${INCLUDE_DIR})

cuda_add_library(cuda_proj_lib ${SOURCES_LIB})
cuda_add_executable(cuda_proj ${SOURCE_DIR}/main.cu)
target_link_libraries(cuda_proj cuda_proj_lib)

set_target_properties(cuda_proj PROPERTIES
        RUNTIME_OUTPUT_DIRECTORY_DEBUG ${CMAKE_BINARY_DIR}/${CMAKE_BUILD_TYPE}
        RUNTIME_OUTPUT_DIRECTORY_RELEASE ${CMAKE_BINARY_DIR}/${CMAKE_BUILD_TYPE}
        )