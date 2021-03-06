cmake_minimum_required(VERSION 3.1)
set(CMAKE_MODULE_PATH ${CMAKE_CURRENT_SOURCE_DIR}/cmake)

option(BUILD_EXAMPLE "Build program example" OFF)
option(BUILD_COVERAGE "Enable coverage" OFF)

set(LIB proc)

project(${PROJECT} CXX)

add_compile_options(-Wall -Wextra -pedantic -std=c++17 -g3)

# Lib
add_subdirectory(src/)
add_subdirectory(include/)

# Uninstall
if(NOT TARGET uninstall)
  configure_file(
    "${CMAKE_MODULE_PATH}/uninstall.cmake.in"
    "${CMAKE_MODULE_PATH}/uninstall.cmake"
    IMMEDIATE @ONLY
  )

  add_custom_target(uninstall
    COMMAND ${CMAKE_COMMAND} -P ${CMAKE_MODULE_PATH}/uninstall.cmake)
endif()


# Example
IF(BUILD_EXAMPLE)
  add_subdirectory(example/src)
ENDIF(BUILD_EXAMPLE)

# Tests
enable_testing()
add_subdirectory(tests)

IF(CMAKE_EXPORT_COMPILE_COMMANDS)
  add_custom_target(clang-tidy COMMAND "run-clang-tidy.py"
    "-header-filter='${CMAKE_CURRENT_SOURCE_DIR}/include/.*'"
    "-checks=-*,modernize-*,readability-*,performance-*,-readability-named-parameter"
    COMMENT "Running clang-tidy static checks")
ENDIF(CMAKE_EXPORT_COMPILE_COMMANDS)
