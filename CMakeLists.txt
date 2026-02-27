cmake_minimum_required(VERSION 3.20)

project(libshout)

add_library(libshout)

option(SHOUT_WITH_SPEEX "WITH_SPEEX" OFF)
option(SHOUT_WITH_OPENSSL "WITH_OPENSSL" OFF)
option(SHOUT_WITH_THEORA "WITH_THEORA" OFF)
option(SHOUT_WITH_OGG "WITH_OGG" OFF)
option(SHOUT_WITH_VORBIS "WITH_VORBIS" OFF)
option(SHOUT_WITHOUT_PTHREAD "WITHOUT_PTHREAD" OFF)

# Inspired from /usr/share/autoconf/autoconf/c.m4
foreach(KEYWORD "inline" "__inline__" "__inline")
    if(NOT DEFINED C_INLINE)
        try_compile(C_HAS_${KEYWORD} "${CMAKE_CURRENT_SOURCE_DIR}"
            "${CMAKE_CURRENT_SOURCE_DIR}/test_inline.c"
            COMPILE_DEFINITIONS "-Dinline=${KEYWORD}")
        if(C_HAS_${KEYWORD})
            set(C_INLINE TRUE)
            target_compile_definitions(${PROJECT_NAME} PRIVATE "-Dinline=${KEYWORD}")
        endif(C_HAS_${KEYWORD})
    endif(NOT DEFINED C_INLINE)
endforeach(KEYWORD)

if(NOT DEFINED C_INLINE)
    target_compile_definitions(${PROJECT_NAME} PRIVATE "-Dinline=")
endif()

include_directories(${PROJECT_NAME}
    PUBLIC
        ${CMAKE_CURRENT_SOURCE_DIR}/include
    PRIVATE
        ${CMAKE_CURRENT_SOURCE_DIR}/src
        ${CMAKE_CURRENT_SOURCE_DIR}/src/common
)

if(WIN32)
    include_directories(${PROJECT_NAME}
        PRIVATE
            ${CMAKE_CURRENT_SOURCE_DIR}/win32compat
            ${CMAKE_CURRENT_SOURCE_DIR}/win32compat/shout
    )
endif()

if(WIN32)
    target_compile_definitions(${PROJECT_NAME} PRIVATE "-Dstrcasecmp=_stricmp")
else()
    configure_file(${CMAKE_CURRENT_SOURCE_DIR}/include/shout/shout.h.in ${CMAKE_CURRENT_SOURCE_DIR}/include/shout/shout.h)
endif()

include(CheckIncludeFiles)
include(CheckFunctionExists)
include(CheckIncludeFile)

CHECK_FUNCTION_EXISTS(endhostent HAVE_ENDHOSTENT)
if(HAVE_ENDHOSTENT)
    target_compile_definitions(${PROJECT_NAME} PRIVATE -DHAVE_ENDHOSTENT)
endif()

CHECK_FUNCTION_EXISTS(ftime HAVE_FTIME)
if(HAVE_FTIME)
    target_compile_definitions(${PROJECT_NAME} PRIVATE -DHAVE_FTIME)
endif()

CHECK_FUNCTION_EXISTS(getaddrinfo HAVE_GETADDRINFO)
if(HAVE_GETADDRINFO)
    target_compile_definitions(${PROJECT_NAME} PRIVATE -DHAVE_GETADDRINFO)
endif()

CHECK_FUNCTION_EXISTS(getnameinfo HAVE_GETNAMEINFO)
if(HAVE_GETNAMEINFO)
    target_compile_definitions(${PROJECT_NAME} PRIVATE -DHAVE_GETNAMEINFO)
endif()

CHECK_FUNCTION_EXISTS(gettimeofday HAVE_GETTIMEOFDAY)
if(HAVE_GETTIMEOFDAY)
    target_compile_definitions(${PROJECT_NAME} PRIVATE -DHAVE_GETTIMEOFDAY)
endif()


CHECK_FUNCTION_EXISTS(inet_aton HAVE_INET_ATON)
if(HAVE_INET_ATON)
    target_compile_definitions(${PROJECT_NAME} PRIVATE -DHAVE_INET_ATON)
endif()

CHECK_FUNCTION_EXISTS(inet_pton HAVE_INET_PTON)
if(HAVE_INET_PTON)
    target_compile_definitions(${PROJECT_NAME} PRIVATE -DHAVE_INET_PTON)
endif()

CHECK_INCLUDE_FILE(inttypes.h HAVE_INTTYPES_H)
if(HAVE_INTTYPES_H)
    target_compile_definitions(${PROJECT_NAME} PRIVATE -DHAVE_INTTYPES_H)
endif()

CHECK_INCLUDE_FILE(stdint.h HAVE_STDINT_H)
if(HAVE_STDINT_H)
    target_compile_definitions(${PROJECT_NAME} PRIVATE -DHAVE_STDINT_H)
endif()

CHECK_INCLUDE_FILE(memory.h HAVE_MEMORY_H)
if(HAVE_MEMORY_H)
    target_compile_definitions(${PROJECT_NAME} PRIVATE -DHAVE_MEMORY_H)
endif()

CHECK_FUNCTION_EXISTS(nanosleep HAVE_NANOSLEEP)
if(HAVE_NANOSLEEP)
    target_compile_definitions(${PROJECT_NAME} PRIVATE -DHAVE_NANOSLEEP)
endif()

CHECK_INCLUDE_FILE(pthread.h HAVE_PTHREAD)
if(HAVE_PTHREAD)
    target_compile_definitions(${PROJECT_NAME} PRIVATE -DHAVE_PTHREAD)
endif()

if(SHOUT_WITHOUT_PTHREAD)
    set(HAVE_PTHREAD OFF)
endif()

if(NOT HAVE_PTHREAD)
    target_compile_definitions(${PROJECT_NAME} PRIVATE -DNO_THREAD)
endif()

CHECK_FUNCTION_EXISTS(pthread_spin_lock HAVE_PTHREAD_SPIN_LOCK)
if(HAVE_PTHREAD_SPIN_LOCK)
    target_compile_definitions(${PROJECT_NAME} PRIVATE -DHAVE_PTHREAD_SPIN_LOCK)
endif()

CHECK_FUNCTION_EXISTS(sethostent HAVE_SETHOSTENT)
if(HAVE_SETHOSTENT)
    target_compile_definitions(${PROJECT_NAME} PRIVATE -DHAVE_SETHOSTENT)
endif()

CHECK_INCLUDE_FILE(stdlib.h HAVE_STDLIB_H)
if(HAVE_STDLIB_H)
    target_compile_definitions(${PROJECT_NAME} PRIVATE -DHAVE_STDLIB_H)
endif()

CHECK_INCLUDE_FILE(strings.h HAVE_STRINGS_H)
if(HAVE_STRINGS_H)
    target_compile_definitions(${PROJECT_NAME} PRIVATE -DHAVE_STRINGS_H)
endif()

CHECK_INCLUDE_FILE(string.h HAVE_STRING_H)
if(HAVE_STRING_H)
    target_compile_definitions(${PROJECT_NAME} PRIVATE -DHAVE_STRING_H)
endif()

CHECK_INCLUDE_FILE(sys/select.h HAVE_SYS_SELECT_H)
if(HAVE_SYS_SELECT_H)
    target_compile_definitions(${PROJECT_NAME} PRIVATE -DHAVE_SYS_SELECT_H)
endif()

CHECK_INCLUDE_FILE(sys/socket.h HAVE_SYS_SOCKET_H)
if(HAVE_SYS_SOCKET_H)
    target_compile_definitions(${PROJECT_NAME} PRIVATE -DHAVE_SYS_SOCKET_H)
endif()

CHECK_INCLUDE_FILE(sys/stat.h HAVE_SYS_STAT_H)
if(HAVE_SYS_STAT_H)
    target_compile_definitions(${PROJECT_NAME} PRIVATE -DHAVE_SYS_STAT_H)
endif()

CHECK_INCLUDE_FILE(sys/timeb.h HAVE_SYS_TIMEB_H)
if(HAVE_SYS_TIMEB_H)
    target_compile_definitions(${PROJECT_NAME} PRIVATE -DHAVE_SYS_TIMEB_H)
endif()

CHECK_INCLUDE_FILE(sys/types.h HAVE_SYS_TYPES_H)
if(HAVE_SYS_TYPES_H)
    target_compile_definitions(${PROJECT_NAME} PRIVATE -DHAVE_SYS_TYPES_H)
endif()

CHECK_INCLUDE_FILE(sys/uio.h HAVE_SYS_UIO_H)
if(HAVE_SYS_UIO_H)
    target_compile_definitions(${PROJECT_NAME} PRIVATE -DHAVE_SYS_UIO_H)
endif()

CHECK_INCLUDE_FILE(unistd.h HAVE_UNISTD_H)
if(HAVE_UNISTD_H)
    target_compile_definitions(${PROJECT_NAME} PRIVATE -DHAVE_UNISTD_H)
endif()


CHECK_INCLUDE_FILE(winsock2.h HAVE_WINSOCK2_H)
if(HAVE_WINSOCK2_H)
    target_compile_definitions(${PROJECT_NAME} PRIVATE -DHAVE_WINSOCK2_H)
endif()

CHECK_FUNCTION_EXISTS(writev HAVE_WRITEV)
if(HAVE_WRITEV)
    target_compile_definitions(${PROJECT_NAME} PRIVATE -DHAVE_WRITEV)
endif()

set(PACKAGE_URL https://github.com/Iunusov/libshout-CMAKE)
set(PACKAGE_BUGREPORT https://github.com/Iunusov/libshout-CMAKE)
target_compile_definitions(${PROJECT_NAME} PRIVATE -DVERSION="2.4.1")
target_compile_definitions(${PROJECT_NAME} PRIVATE -DLIBSHOUT_MAJOR=2)
target_compile_definitions(${PROJECT_NAME} PRIVATE -DLIBSHOUT_MINOR=4)
target_compile_definitions(${PROJECT_NAME} PRIVATE -DLIBSHOUT_MICRO=1)
target_compile_definitions(${PROJECT_NAME} PRIVATE -DHAVE_GETTIMEOFDAY=1)
target_compile_definitions(${PROJECT_NAME} PRIVATE -DTIME_WITH_SYS_TIME)

if(HAVE_PTHREAD)
    aux_source_directory(${CMAKE_CURRENT_SOURCE_DIR}/src/common/thread SOURCE_LIB)
endif()

aux_source_directory(${CMAKE_CURRENT_SOURCE_DIR}/src/common/net SOURCE_LIB)
aux_source_directory(${CMAKE_CURRENT_SOURCE_DIR}/src/common/httpp SOURCE_LIB)
aux_source_directory(${CMAKE_CURRENT_SOURCE_DIR}/src/common/avl SOURCE_LIB)
aux_source_directory(${CMAKE_CURRENT_SOURCE_DIR}/src/common/timing SOURCE_LIB)
aux_source_directory(${CMAKE_CURRENT_SOURCE_DIR}/src SOURCE_LIB)

if (NOT SHOUT_WITH_OPENSSL)
    list(REMOVE_ITEM SOURCE_LIB "${CMAKE_CURRENT_SOURCE_DIR}/src/tls.c")
endif()

if(NOT SHOUT_WITH_SPEEX)
    list(REMOVE_ITEM SOURCE_LIB "${CMAKE_CURRENT_SOURCE_DIR}/src/codec_speex.c")
endif()

if(NOT SHOUT_WITH_THEORA)
    list(REMOVE_ITEM SOURCE_LIB "${CMAKE_CURRENT_SOURCE_DIR}/src/codec_theora.c")
endif()

if(NOT SHOUT_WITH_OGG)
    list(REMOVE_ITEM SOURCE_LIB "${CMAKE_CURRENT_SOURCE_DIR}/src/codec_opus.c")
    list(REMOVE_ITEM SOURCE_LIB "${CMAKE_CURRENT_SOURCE_DIR}/src/format_ogg.c")
endif()

if(NOT SHOUT_WITH_VORBIS)
    list(REMOVE_ITEM SOURCE_LIB "${CMAKE_CURRENT_SOURCE_DIR}/src/codec_vorbis.c")
endif()


list(REMOVE_ITEM SOURCE_LIB "${CMAKE_CURRENT_SOURCE_DIR}/src/common/avl/test.c")

if(WIN32)
    list(APPEND SOURCE_LIB "${CMAKE_CURRENT_SOURCE_DIR}/win32compat/gettimeofday.c")
endif()

target_sources(${PROJECT_NAME} PRIVATE ${SOURCE_LIB})

target_include_directories(${PROJECT_NAME}
    PUBLIC ${CMAKE_CURRENT_SOURCE_DIR}/include
)

if(SHOUT_WITH_VORBIS)
    target_link_libraries(${PROJECT_NAME} PRIVATE libvorbis)
endif()

if(SHOUT_WITH_OGG)
    target_link_libraries(${PROJECT_NAME} PRIVATE libogg)
endif()

if(SHOUT_WITH_SPEEX)
    target_link_libraries(${PROJECT_NAME} PRIVATE speex)
endif()

if(SHOUT_WITH_THEORA)
    target_link_libraries(${PROJECT_NAME} PRIVATE theora)
endif()

if(SHOUT_WITH_OPENSSL)
    target_link_libraries(${PROJECT_NAME} PRIVATE ssl)
endif()

if(WIN32)
    target_link_libraries(${PROJECT_NAME} PRIVATE wsock32 ws2_32)
endif()

set_property(TARGET ${PROJECT_NAME} PROPERTY POSITION_INDEPENDENT_CODE ON)
