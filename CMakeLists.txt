################################################################################
#
# CMakeLists.txt
# 
# Author: Anthony Cabrera:
# Contact: cabreraam AT ieee DOT org
# Description: CMakeLists.txt replacement for SNAP Makefile
#
################################################################################

cmake_minimum_required(VERSION 3.18)

# if this is not being used in llvm-test-suite
if(NOT PROJECT_NAME STREQUAL "test-suite")
	message(STATUS "${PROJECT_NAME} - SNAP not in test-suite")
	project(snap_test)
	set(CMAKE_RUNTIME_OUTPUT_DIRECTORY ${CMAKE_BINARY_DIR}/bin)
else()
	message(STATUS "${PROJECT_NAME} - SNAP in test-suite")
endif()

# Direct CMake to a specific MPI implementation. Specify
# -DMPI_EXECUTABLE_SUFFIX=.mpich for MPICH MPI implementation 
set(MPI_EXECUTABLE_SUFFIX "" CACHE STRING "Direct CMake to a specific MPI implementation")

enable_language(Fortran)


add_subdirectory(src)
