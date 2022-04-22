################################################################################
#
# CMakeLists.txt
# 
# Author: Anthony Cabrera:
# Contact: cabreraam AT ieee DOT org
# Description: CMakeLists.txt replacement for SNAP Makefile
#
################################################################################

cmake_minimum_required(VERSION 3.16.3)

project(snap_test)

enable_language(Fortran)

set(CMAKE_RUNTIME_OUTPUT_DIRECTORY ${CMAKE_BINARY_DIR}/bin)

add_subdirectory(src)
