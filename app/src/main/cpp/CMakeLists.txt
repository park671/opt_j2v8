# For more information about using CMake with Android Studio, read the
# documentation: https://d.android.com/studio/projects/add-native-code.html

# Sets the minimum version of CMake required to build the native library.

cmake_minimum_required(VERSION 3.18.1)

# Declares and names the project.

project("j2v8")

set(CMAKE_VERBOSE_MAKEFILE ON)

# set up the module path
set(CMAKE_MODULE_PATH ${PROJECT_SOURCE_DIR}/cmake)
include(BuildUtils)
include(Policies)

set(J2V8_V8_DIR "${CMAKE_SOURCE_DIR}/v8.out" CACHE STRING "Path to the V8 JS engine dependency")

# project include directories
set(include_dirs
        ${J2V8_V8_DIR}/include
        )

set(src_files
        com_eclipsesource_v8_V8Impl.cpp
        com_eclipsesource_v8_V8Impl.h
        )
source_group("" FILES ${src_files})
# tell gcc/clang to use the c++14 standard
set(CMAKE_CXX_STANDARD 17)

set(CMAKE_C_FLAGS "${CMAKE_C_FLAGS} -Wall -Wno-unused-function -Wno-unused-variable -O3 -funroll-loops -ftree-vectorize -ffast-math -fpermissive -fPIC ")
set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -Wall -Wno-unused-function -Wno-unused-variable -O3 -funroll-loops -ftree-vectorize -ffast-math -fpermissive -fPIC ")

# create the j2v8 library
add_library(v8_monolith STATIC IMPORTED)
set_target_properties(v8_monolith PROPERTIES IMPORTED_LOCATION ${J2V8_V8_DIR}/${ANDROID_ABI}/libv8_monolith.a)

add_library(j2v8 SHARED ${src_files})

# set the include directories
include_directories(${include_dirs})

# link the necessary libraries
target_link_libraries(j2v8
        v8_monolith
        )