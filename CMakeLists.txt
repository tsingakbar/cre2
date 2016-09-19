cmake_minimum_required(VERSION 3.5)
if(NOT DEFINED RE2_LIB_DIR)
  message( FATAL_ERROR "RE2_LIB_DIR must be defined." )
endif()
if(NOT DEFINED RE2_HEADER_DIR)
  message( FATAL_ERROR "RE2_HEADER_DIR must be defined." )
endif()
include_directories(${RE2_HEADER_DIR})
add_library(cre2 STATIC src/cre2.cpp)
target_link_libraries(cre2 ${RE2_LIB_DIR}/libre2.a)
get_property(known_features GLOBAL PROPERTY CMAKE_CXX_KNOWN_FEATURES)
set(needed_features
  cxx_deleted_functions
  cxx_strong_enums
  cxx_constexpr
  cxx_auto_type)
target_compile_features(cre2 PRIVATE ${needed_features})
add_definitions(-Dcre2_VERSION_INTERFACE_CURRENT=0)
add_definitions(-Dcre2_VERSION_INTERFACE_REVISION=0)
add_definitions(-Dcre2_VERSION_INTERFACE_AGE=0)
add_definitions(-Dcre2_VERSION_INTERFACE_STRING="0.0")
