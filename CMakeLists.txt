cmake_minimum_required(VERSION 3.4...3.18)

project(_re2)
include(GNUInstallDirs)

set(CMAKE_CXX_STANDARD 11)
set(CMAKE_CXX_STANDARD_REQUIRED ON)
set(CMAKE_CXX_EXTENSIONS OFF)

if(CMAKE_CXX_COMPILER_ID STREQUAL Clang)
  set(CLANG_DEFAULT_CXX_STDLIB libc++)
  set(CLANG_DEFAULT_RTLIB compiler-rt)
endif()

find_package(re2 CONFIG REQUIRED NAMES re2)
find_package(Threads REQUIRED)

find_package(pybind11 CONFIG REQUIRED)
pybind11_add_module(_re2 _re2.cc)

# SCM_VERSION_INFO is defined by setup.py and passed into the C++ code as a
# define (VERSION_INFO) here.
target_compile_definitions(_re2 PRIVATE VERSION_INFO=${SCM_VERSION_INFO})

target_compile_features(_re2 INTERFACE cxx_std_11)
target_link_libraries(_re2 PRIVATE re2::re2)

message(STATUS "Set CMAKE_BUILD_OVERRIDE to change build type.")
message(STATUS "<<< Build configuration >>>
Build type      ${CMAKE_BUILD_TYPE}
Build override  ${CMAKE_BUILD_OVERRIDE}
Install path    ${CMAKE_INSTALL_PREFIX}
Compiler flags:
C               ${CMAKE_C_FLAGS}
C++             ${CMAKE_CXX_FLAGS}
Linker flags:
Executable      ${CMAKE_EXE_LINKER_FLAGS}
Module          ${CMAKE_MODULE_LINKER_FLAGS}
Shared          ${CMAKE_SHARED_LINKER_FLAGS}

ToolchainFile:
    ${CMAKE_TOOLCHAIN_FILE}\n")
