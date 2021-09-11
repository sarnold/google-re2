cmake_minimum_required(VERSION 3.18)

project(_re2 LANGUAGES CXX)

set(CMAKE_EXPORT_COMPILE_COMMANDS ON)
set(CMAKE_VERBOSE_MAKEFILE ON)

set(CMAKE_CXX_STANDARD 11)
set(CMAKE_CXX_STANDARD_REQUIRED ON)
set(CMAKE_CXX_EXTENSIONS OFF)

if(CMAKE_CXX_COMPILER_ID STREQUAL Clang)
  set(CLANG_DEFAULT_CXX_STDLIB libc++)
  set(CLANG_DEFAULT_RTLIB compiler-rt)
endif()

include(GNUInstallDirs)

# here we get to jump through some hoops to find libre2 on the manylinux
# docker CI images, etc
find_package(re2 CONFIG NAMES re2)

if(re2_FOUND)
  message(STATUS "System re2 found")
  set(EXTERNAL_LIBS re2::re2)
elseif(NOT MSVC)
  message(STATUS "Trying PkgConfig")
  find_package(PkgConfig REQUIRED)
  pkg_check_modules(RE2 IMPORTED_TARGET re2)

  if(RE2_FOUND)
    include_directories(${RE2_INCLUDE_DIRS})
    set(EXTERNAL_LIBS PkgConfig::RE2)
  else()
    # last resort for manylinux: just try it
    message(STATUS "Blindly groping instead")
    link_directories("/usr/lib64" "/usr/lib")
    set(EXTERNAL_LIBS "libre2.so")
  endif()
endif()

find_package(Threads REQUIRED)

find_package(pybind11 CONFIG)

if(pybind11_FOUND)
  message(STATUS "System pybind11 found")
else()
  message(STATUS "Fetching pybind11 from github")
  # Fetch pybind11
  include(FetchContent)

  FetchContent_Declare(
    pybind11
    GIT_REPOSITORY https://github.com/pybind/pybind11
    GIT_TAG v2.6.1
  )
  FetchContent_MakeAvailable(pybind11)
endif()

pybind11_add_module(_re2 _re2.cc)

# SCM_VERSION_INFO is defined by setup.py and passed into the C++ code as a
# define (VERSION_INFO) here.
target_compile_definitions(_re2 PRIVATE VERSION_INFO=${SCM_VERSION_INFO})
target_compile_features(_re2 INTERFACE cxx_std_11)
target_link_libraries(_re2 PRIVATE ${EXTERNAL_LIBS})

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
