cmake_minimum_required(VERSION 3.4...3.18)
project(_re2)

find_package(re2 CONFIG REQUIRED)
find_package(Threads REQUIRED)

find_package(pybind11 CONFIG REQUIRED)
pybind11_add_module(_re2 _re2.cc)

set(CMAKE_CXX_STANDARD 11)
set(CMAKE_CXX_STANDARD_REQUIRED ON)
set(CMAKE_CXX_EXTENSIONS OFF)

# EXAMPLE_VERSION_INFO is defined by setup.py and passed into the C++ code as a
# define (VERSION_INFO) here.
target_compile_definitions(_re2 PRIVATE VERSION_INFO=${EXAMPLE_VERSION_INFO})

target_compile_features(_re2 INTERFACE cxx_std_11)
target_link_libraries(_re2 PRIVATE re2::re2)
