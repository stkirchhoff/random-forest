cmake_minimum_required(VERSION 2.6)
project(ml)
include_directories(include)
file(GLOB headers include/andres/* include/andres/ml/*)

##############################################################################
# Gnu g++ specific commands
##############################################################################
if(CMAKE_COMPILER_IS_GNUCXX)
   set(ENABLE_CXX11 "-std=c++11")
   execute_process(
       COMMAND "${CMAKE_CXX_COMPILER} -dumpversion"
       OUTPUT_VARIABLE GCC_VERSION
   )
   if(GCC_VERSION VERSION_LESS 4.7)
      set(ENABLE_CXX11 "-std=c++0x")
   endif()
   set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} ${ENABLE_CXX11}")
endif()

##############################################################################
# Doxygen
##############################################################################
find_package(Doxygen QUIET)
if(DOXYGEN_FOUND)
    message(STATUS "Doxygen found")
else()
    message("doxygen not found")
endif()

##############################################################################
# MSVC-specific settings
##############################################################################
if(MSVC)
    add_definitions(-D_CRT_SECURE_NO_WARNINGS)
endif()

##############################################################################
# OpenMP
##############################################################################
find_package(OpenMP)
if(OPENMP_FOUND)
    set(CMAKE_C_FLAGS "${CMAKE_C_FLAGS} ${OpenMP_C_FLAGS}")
    set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} ${OpenMP_CXX_FLAGS}")
    message(STATUS "OpenMP found")
else()
    message("OpenMP not found")
endif()

##############################################################################
# targets
##############################################################################
add_executable(test-decision-trees src/unittest/ml/test-decision-trees.cxx ${headers})
add_test(test-decision-trees test-decision-trees)

if(DOXYGEN_FOUND)
    configure_file("${ml_SOURCE_DIR}/doxygen/doxyfile-ml-decision-trees.in" "${ml_BINARY_DIR}/doxyfile-ml-decision-trees" @ONLY IMMEDIATE)
    add_custom_target(doc-ml-decision-trees ALL COMMAND ${DOXYGEN} "${ml_BINARY_DIR}/doxyfile-ml-decision-trees")
endif()
