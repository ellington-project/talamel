cmake_minimum_required (VERSION 3.0)

project (TALAMEL)

# Compile taglib
include(ExternalProject)

# set(TALAMEL_SOURCE_DIR ${CMAKE_CURRENT_SOURCE_DIR})
set(TAGLIB_DIR "${CMAKE_BINARY_DIR}/taglib")
set(TAGLIB_INSTALL "${TAGLIB_DIR}/install")

ExternalProject_Add(
    taglib 
    GIT_REPOSITORY git@github.com:taglib/taglib.git
    PREFIX ${TAGLIB_DIR} 
    CMAKE_ARGS -DCMAKE_INSTALL_PREFIX=${TAGLIB_INSTALL} -DBUILD_SHARED_LIBS=OFF -DENABLE_STATIC_RUNTIME=ON -DCMAKE_C_FLAGS=-fPIC -Wall -O3
)

include_directories(${TAGLIB_INSTALL}/include)
link_directories(${TAGLIB_INSTALL}/lib)

# Add the talamel library
# set(talamel_headers talamel.h)

add_library(talamel STATIC
    src/talamel.cpp 
    # ${talamel_headers}
)
target_include_directories(talamel
    PUBLIC 
    ${CMAKE_SOURCE_DIR}/include
)
add_dependencies(talamel taglib)
target_link_libraries(talamel tag z)
set_target_properties(talamel PROPERTIES PUBLIC_HEADER "${talamel_headers}")

# Link in the c++ runtime here at some point?

# set_target_properties(talamel PROPERTIES
#     VERSION 0.0.0
#     SOVERSION 0 
#     DEFINE_SYMBOL MAKE_TALAMEL_C_LIB
#     INSTALL_NAME_DIR ${LIB_INSTALL_DIR}
# )
# install(TARGETS talamel
#     ARCHIVE DESTINATION ${LIB_INSTALL_DIR}
#     LIBRARY DESTINATION ${LIB_INSTALL_DIR}
#     PUBLIC_HEADER DESTINATION ${INCLUDE_INSTALL_DIR}/talamel
# )

# Add an app depending on that
add_executable(Readmeta apps/readmeta.c)
add_dependencies(Readmeta talamel)
target_link_libraries(Readmeta talamel) 