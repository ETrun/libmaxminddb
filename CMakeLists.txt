cmake_minimum_required(VERSION 2.6)

project(mmdb C)

set(MMDB_SOURCES
    ${CMAKE_CURRENT_SOURCE_DIR}/src/maxminddb.c
    ${CMAKE_CURRENT_SOURCE_DIR}/src/data-pool.c
)

if(MMDB_BUILD_STATIC)
    set(MMDB_LIBTYPE STATIC)
else()
    set(MMDB_LIBTYPE SHARED)
endif()

add_library(mmdb ${MMDB_LIBTYPE} ${MMDB_SOURCES})
set_property(TARGET mmdb PROPERTY POSITION_INDEPENDENT_CODE ON)

if(WIN32)
    set(MMDB_INCLUDE_DIRECTORIES
        ${CMAKE_CURRENT_SOURCE_DIR}/src
        ${CMAKE_CURRENT_SOURCE_DIR}/include
        ${CMAKE_CURRENT_SOURCE_DIR}/projects/VS12
    )
    target_link_libraries(mmdb ws2_32)
else(WIN32)
    set(MMDB_INCLUDE_DIRECTORIES
        ${CMAKE_CURRENT_SOURCE_DIR}/src
        ${CMAKE_CURRENT_SOURCE_DIR}/include
    )
    configure_file(
        "${PROJECT_SOURCE_DIR}/include/maxminddb_config.h.in"
        "${PROJECT_SOURCE_DIR}/include/maxminddb_config.h"
    )
    add_definitions(-DPACKAGE_VERSION="1.3.2")
    add_definitions(-DMMDB_UINT128_IS_BYTE_ARRAY=1)
endif()

include_directories(${MMDB_INCLUDE_DIRECTORIES})

target_include_directories(mmdb PUBLIC "${MMDB_INCLUDE_DIRECTORIES}")

if(OSX_UNIVERSAL_BINARIES)
    set_target_properties(mmdb PROPERTIES OSX_ARCHITECTURES "i386;x86_64")
endif()
