cmake_minimum_required(VERSION 3.8)
project(hpp_skel LANGUAGES CXX)
set(CMAKE_CXX_STANDARD 11)
set(CMAKE_CXX_STANDARD_REQUIRED on)
set(CMAKE_EXPORT_COMPILE_COMMANDS ON)

option(WERROR "Add -Werror flag to build (turns warnings into errors)" ON)

# configure optimization
if (CMAKE_BUILD_TYPE STREQUAL "Debug")
    set(OPTIMIZATION_FLAGS "-O0 -DDEBUG")
    message("-- Configuring debug build")
else()
    set(OPTIMIZATION_FLAGS "-O3 -DNDEBUG")
    message("-- Configuring release build")
endif()

# Enable extra warnings to adhere to https://github.com/mapbox/cpp/issues/37
set(DESIRED_WARNINGS "-Wall -Wextra -Wconversion -Wunreachable-code -Wuninitialized -pedantic-errors -Wold-style-cast -Wno-error=unused-variable -Wshadow -Wfloat-equal -Weffc++")
if(CMAKE_CXX_COMPILER_ID STREQUAL "Clang")
  set(DESIRED_WARNINGS "${DESIRED_WARNINGS} -Wmost")
endif()

set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} ${OPTIMIZATION_FLAGS} ${DESIRED_WARNINGS}")

if (WERROR)
    set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -Werror")
endif()

find_package(Boost 1.69 REQUIRED COMPONENTS filesystem system)
include_directories(SYSTEM ${Boost_INCLUDE_DIRS})

find_package(RapidJSON)

include_directories(SYSTEM /usr/include/catch)

include_directories("${PROJECT_SOURCE_DIR}/include")

file(GLOB TEST_SOURCES tests/unit/*.cpp)
add_executable(unit-tests ${TEST_SOURCES})

file(GLOB TEST_SOURCES tests/fixtures/*.cpp)
add_executable(fixture-tests ${TEST_SOURCES})

file(GLOB FUZZER_SOURCES fuzzer/*.cpp)
add_executable(fuzzer-tests ${FUZZER_SOURCES})
