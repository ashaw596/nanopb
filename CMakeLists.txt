project(nanopb)

if(${COMPILE_MBED})
# set the arm toolchain and include it so we can use it
set(CMAKE_TOOLCHAIN_FILE ${ARM_TOOLCHAIN_FILE})
include(${ARM_TOOLCHAIN_FILE})

# Set compiler and linker flags
set(CMAKE_CXX_FLAGS         ${MBED_CMAKE_CXX_FLAGS}         )
set(CMAKE_C_FLAGS           ${MBED_CMAKE_C_FLAGS}           )
set(CMAKE_EXE_LINKER_FLAGS  ${MBED_CMAKE_EXE_LINKER_FLAGS}  )
endif()

# append all of the accessory library sources

cmake_minimum_required(VERSION 2.8)

set(nanopb_VERSION_STRING nanopb-0.3.7)

string(REPLACE "nanopb-" "" nanopb_VERSION ${nanopb_VERSION_STRING})



add_custom_target(
    protoc-nanopb
    COMMAND make
    WORKING_DIRECTORY ${CMAKE_CURRENT_SOURCE_DIR}/generator/proto
)
add_library(libprotobuf-nanopb STATIC
    pb.h
    pb_common.h
    pb_common.c
    pb_encode.h
    pb_encode.c
    pb_decode.h
    pb_decode.c)

add_dependencies(libprotobuf-nanopb protoc-nanopb)

add_dependencies(libprotobuf-nanopb mbed_libraries ${MBED_ASSEC_LIBS_DEPENDS})

target_link_libraries(libprotobuf-nanopb ${MBED_LIBRARY})

target_include_directories(libprotobuf-nanopb INTERFACE
  $<INSTALL_INTERFACE:${CMAKE_INSTALL_INCLUDEDIR}>
)

if(${COMPILE_MBED})
target_include_directories(libprotobuf-nanopb PUBLIC ${MBED_ASSEC_LIBS_SRCS})
endif()

if(NOT DEFINED CMAKE_INSTALL_CMAKEDIR)
    set(CMAKE_INSTALL_CMAKEDIR "lib/cmake/nanopb")
endif()

