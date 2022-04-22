################################################################################
#
# CMakeLists.txt
# 
# Author: Anthony Cabrera:
# Contact: cabreraam AT ieee DOT org
# Description: CMakeLists.txt replacement for SNAP Makefile
#
################################################################################

#cmake_minimum_required(VERSION 3.16.3)

#project(snap_test)

#enable_language(Fortran)

# TODO: setup install directory in build/bin

message(STATUS ${CMAKE_Fortran_COMPILER_LIST})

################################################################################
#
# Options and Variables
#
################################################################################

option(MPI "Use MPI" OFF)
option(OPT "Turn on optimizations" ON)
option(OPENMP "Enable OpenMP" OFF)
option(KNL "Intel KNL target" OFF)
option(HASWELL "Does the CPU use the Haswell u-arch" OFF)

if(MPI)
	find_package(MPI REQUIRED)
endif()

#set(MPI "" CACHE STRING "Configure MPI")
#set(FFLAGS "" CACHE STRING "Fortran flags")
#set(FFLAG2 "" CACHE STRING "Fortran flags 2?")
#set(DEFS "" CACHE STRING "Compiler definitions")
#set(PPFLAGS "" CACHE STRING "Preprocessor flags?")
#set(FORTRAN "" CACHE STRING "Fortran compiler")

# Compiler Preprocessor Definitions
# These should be passed in at the command line as a semi-colon-separated list
# If the developer wants MPI, it would be set here
# e.g., MPI + other pound define --> -DUSER_DEFS="MPI;OTHER_VAR;OTHER_VAR2=1"
set(USER_DEFS "" CACHE STRING "Semi-colon separted preprocessor pound defines")

################################################################################
#
# Compile Options for Different Compilers
#
################################################################################

# Compile Options for GNU
set(SNAP_COMPILER_OPTIONS_GNU_OPT
	"-O3" 
	CACHE 
	STRING 
	"GNU compiler options when OPT=ON"
)
set(SNAP_COMPILER_OPTIONS_GNU_NOT_OPT
	"-O0"
	"-g"
	"-fbounds-check"
	"-fbacktrace"
	CACHE 
	STRING 
	"GNU compiler options when OPT=OFF"
)
set(SNAP_COMPILER_OPTIONS_GNU
	$<$<BOOL:${OPENMP}>:-fopenmp>
	$<IF:$<BOOL:${OPT}>,${SNAP_COMPILER_OPTIONS_GNU_OPT},${SNAP_COMPILER_OPTIONS_GNU_NOT_OPT}>
	CACHE
	STRING
	"Compiler options when GNU compiler detected"
)

# Compile Options for ifort 
set(SNAP_COMPILER_OPTIONS_IFORT_OPT
	"-O3"
	"-ip"
	"-align array32byte"
	"-qno-opt-dynamic-align"
	"-fno-fnalias"
	"-fp-model fast"
	"-fp-speculation fast"
	CACHE 
	STRING 
	"ifort compiler options when OPT=ON"
)
set(SNAP_COMPILER_OPTIONS_INTEL_NOT_OPT
	"-O0"
	"-g "
	"-check bounds"
	"-traceback"
	"-warn unused"
	CACHE 
	STRING 
	"ifort compiler options when OPT=OFF"
)
set(SNAP_COMPILER_OPTIONS_IFORT
	$<$<BOOL:${OPENMP}>:-qopenmp>
	$<IF:$<BOOL:${OPT}>,${SNAP_COMPILER_OPTIONS_IFORT_OPT},${SNAP_COMPILER_OPTIONS_INTEL_NOT_OPT}>
	$<IF:$<BOOL:${HASWELL}>,"-xcore-avx2","-xHost">
	CACHE
	STRING
	"Compiler options when ifort compiler detected"
)

# Compile Options for ifort knl
set(SNAP_COMPILER_OPTIONS_IFORT_KNL_OPT
	"-O3"
	"-xmic-avx512"
	"-ip"
	"-align array64byte"
	"-qno-opt-dynamic-align"
	"-fp-model fast"
	"-fp-speculation fast"
	"-fno-alias -fno-fnalias"
	CACHE 
	STRING 
	"ifort compiler options when OPT=ON and KNL=ON"
)
# This is the same as SNAP_COMPILER_OPTIONS_INTEL_NOT_OPT
#set(SNAP_COMPILER_OPTIONS_IFORT_KNL_NOT_OPT
#)
set(SNAP_COMPILER_OPTIONS_IFORT_KNL
	$<$<BOOL:${OPENMP}>:-qopenmp>
	$<IF:$<BOOL:${OPT}>,${SNAP_COMPILER_OPTIONS_IFORT_KNL_OPT},${SNAP_COMPILER_OPTIONS_INTEL_NOT_OPT}>
	CACHE
	STRING
	"Compiler options when ifort + knl compiler detected"
)

# Compile Options for intel ifx
set(SNAP_COMPILER_OPTIONS_IFX_OPT
	"-xcore-avx2"
	"-Ofast"
	"-funroll-loops"
	"-nostandard-realloc-lhs"
	"-align array32byte"
	CACHE 
	STRING 
	"ifx compiler options when OPT=ON"
)
# This is the same as SNAP_COMPILER_OPTIONS_INTEL_NOT_OPT
#set(SNAP_COMPILER_OPTIONS_IFX_OPT
#)
set(SNAP_COMPILER_OPTIONS_IFX
	$<$<BOOL:${OPENMP}>:-fiopenmp>
	$<IF:$<BOOL:${OPT}>,${SNAP_COMPILER_OPTIONS_IFX_OPT},${SNAP_COMPILER_OPTIONS_INTEL_NOT_OPT}>
	CACHE
	STRING
	"Compiler options when ifx compiler detected"
)

# Compile Options for Classic Flang
# TODO: What would the actual flags be for Classic Flang?
set(SNAP_COMPILER_OPTIONS_CLASSIC_FLANG_OPT
	"-O3" 
	CACHE 
	STRING 
	"Classic Flang compiler options when OPT=ON"
)
set(SNAP_COMPILER_OPTIONS_CLASSIC_FLANG_NOT_OPT
	"-O0"
	"-g "
	"-check bounds"
	"-traceback"
	"-warn unused"
	CACHE 
	STRING 
	"Classic Flang compiler options when OPT=OFF"
)
set(SNAP_COMPILER_OPTIONS_CLASSIC_FLANG_OPT
	$<$<BOOL:${OPENMP}>:-fopenmp>
	$<IF:$<BOOL:${OPT}>,${SNAP_COMPILER_OPTIONS_CLASSIC_FLANG_OPT},${SNAP_COMPILER_OPTIONS_CLASSIC_FLANG_OPT}>
	CACHE
	STRING
	"Compiler options when Classic Flang compiler detected"
)

# TODO: Next steps are to figure out the preprocessing stuff, and then try to
# compile! If it works, we'll then create options for classic flang, then we'll
# go about trying to add the special sauce for LLVM Flang

################################################################################
#
# New Sections
#
################################################################################

set(SNAP_SOURCE_FILES
	global.f90
	snap_main.f90
	utils.f90
	version.f90
	plib.F90
	geom.f90
	sn.f90
	data.f90
	control.f90
	input.f90
	setup.f90
	dealloc.f90
	translv.f90
	solvar.f90
	outer.f90
	expxs.f90
	inner.f90
	sweep.f90
	octsweep.f90
	dim1_sweep.f90
	dim3_sweep.f90
	output.f90
	time.F90
	mms.f90
	analyze.f90
	thrd_comm.f90
	mkba_sweep.f90
	CACHE
	STRING
	"List of SNAP source files"
)

# Handle files that need to be proprocessed, i.e., files with .F90 and NOT .f90
set(SNAP_SOURCE_FILES_TO_PREPROC ${SNAP_SOURCE_FILES})
list(FILTER SNAP_SOURCE_FILES_TO_PREPROC INCLUDE REGEX .*\.F90)
set(SNAP_SOURCE_FILES_TO_PREPROC 
	${SNAP_SOURCE_FILES_TO_PREPROC}
	CACHE
	STRING
	"List of SNAP source files to preprocess"
)
set_source_files_properties(
	${SNAP_SOURCE_FILES_TO_PREPROC}
	PROPERTIES
		Fortran_PREPROCESS ON
)

# TODO: Need to handle LLVM Flang!

# TODO: Wrap in an if statement LLVM Flang because CMake does not support it
add_executable(snap)
target_sources(snap
	PRIVATE
		${SNAP_SOURCE_FILES}
)

target_compile_options(snap 
	PRIVATE
		$<$<Fortran_COMPILER_ID:GNU>:${SNAP_COMPILER_OPTIONS_GNU}>
  	$<$<AND:$<Fortran_COMPILER_ID:Intel>,$<NOT:$<BOOL:${KNL}>>>:${SNAP_COMPILER_OPTIONS_IFORT}>
  	$<$<AND:$<Fortran_COMPILER_ID:Intel>,$<BOOL:${KNL}>>:${SNAP_COMPILER_OPTIONS_IFORT_KNL}>
		$<$<Fortran_COMPILER_ID:IntelLLVM>:${SNAP_COMPILER_OPTIONS_IFX}>
		$<$<Fortran_COMPILER_ID:Flang>:${SNAP_COMPILER_OPTIONS_CLASSIC_FLANG}>

#TODO: Target compile options for Cray with MPI
		#target_compile_options: -L${MPICH_DIR)/lib -lmpich
)
target_compile_definitions(snap
	PRIVATE
		${USER_DEFS}
)
#TODO: Target include options for Cray with MPI
# target_include_directories:	-I$(MPICH_DIR)/include


# TODO: create a custom target to do this line counting operation from Makefile
## Count lines of code
#count:
#	rm -f Lines
#	for file in $(SRCS); do ./LineCount $$file Lines; done
#	awk -f ./LineReport < Lines >> Lines
#	cat Lines

