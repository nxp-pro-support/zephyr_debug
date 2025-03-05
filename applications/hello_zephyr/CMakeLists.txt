# SPDX-License-Identifier: Apache-2.0

cmake_minimum_required(VERSION 3.13.1)

find_package(Zephyr REQUIRED HINTS $ENV{ZEPHYR_BASE})

project(hello_zephyr)

target_sources(app PRIVATE src/main.c)

set(CMAKE_EXPORT_COMPILE_COMMANDS ON)

# colorized errors
zephyr_library_compile_options(-fdiagnostics-color=always)