cmake_minimum_required(VERSION 3.10.2)
project(key-writer
	DESCRIPTION "Key Writer"
	LANGUAGES C
	VERSION 1.0.0)

# To invoke a debug build use: 'cmake -DCMAKE_BUILD_TYPE=Debug ..'
set(CMAKE_C_FLAGS "-Wall -O3 -pedantic") 
set(CMAKE_C_FLAGS_DEBUG "-Wall -O0 -g -DDEBUG")

# -- Mbed TLS --

option(USE_PKCS11_HELPER_LIBRARY "Build mbed TLS with the pkcs11-helper library." OFF)
option(ENABLE_ZLIB_SUPPORT "Build mbed TLS with zlib library." OFF)
option(ENABLE_PROGRAMS "Build mbed TLS programs." OFF)
option(ENABLE_TESTING "Build mbed TLS tests." OFF)

set(CMAKE_INSTALL_NAME_DIR ${CMAKE_INSTALL_FULL_LIBDIR}/lib)
include_directories(${CMAKE_SOURCE_DIR}/external/mbedtls/include)

# Use custom config file for Mbed TLS
add_definitions(-DMBEDTLS_CONFIG_FILE="${CMAKE_SOURCE_DIR}/mbedtls_config.h")

option(USE_STATIC_MBEDTLS_LIBRARY "Build mbed TLS static library." off)
option(USE_SHARED_MBEDTLS_LIBRARY "Build mbed TLS shared library." on)

if (CMAKE_SYSTEM MATCHES "OpenBSD")
	option(LINK_WITH_PTHREAD "Explicitly link mbed TLS library to pthread." on)
endif()

set(MBEDTLS_LIBRARIES "mbedtls")

add_subdirectory(${CMAKE_SOURCE_DIR}/external/mbedtls mbedtls)

# -- Key Writer --

set(SOURCE
	key_writer.c
)

add_executable(key_writer ${SOURCE})

target_link_libraries(key_writer ${MBEDTLS_LIBRARIES})
set_target_properties(key_writer PROPERTIES INSTALL_RPATH mbedtls)
