cmake_minimum_required(VERSION 3.23)
project(libuv LANGUAGES C)

list(APPEND CMAKE_MODULE_PATH "${PROJECT_SOURCE_DIR}/cmake")

set(CMAKE_C_STANDARD 11)
set(CMAKE_C_STANDARD_REQUIRED ON)
set(CMAKE_C_EXTENSIONS ON)

set(_uv_src ${PROJECT_SOURCE_DIR})

set(uv_inc
    ${_uv_src}/include/uv.h
    ${_uv_src}/include/uv/errno.h
    ${_uv_src}/include/uv/threadpool.h
    ${_uv_src}/include/uv/version.h
    ${_uv_src}/include/uv/linux.h
    ${_uv_src}/include/uv/unix.h
)

set(uv_sources
    src/fs-poll.c
    src/idna.c
    src/inet.c
    src/random.c
    src/strscpy.c
    src/strtok.c
    src/thread-common.c
    src/threadpool.c
    src/timer.c
    src/uv-common.c
    src/uv-data-getter-setters.c
    src/version.c
    src/unix/async.c
    src/unix/core.c
    src/unix/dl.c
    src/unix/fs.c
    src/unix/getaddrinfo.c
    src/unix/getnameinfo.c
    src/unix/loop-watcher.c
    src/unix/loop.c
    src/unix/pipe.c
    src/unix/poll.c
    src/unix/process.c
    src/unix/random-devurandom.c
    src/unix/signal.c
    src/unix/stream.c
    src/unix/tcp.c
    src/unix/thread.c
    src/unix/tty.c
    src/unix/udp.c
    src/unix/linux.c
    src/unix/procfs-exepath.c
    src/unix/proctitle.c
    src/unix/random-getrandom.c
    src/unix/random-sysctl-linux.c
)

add_library(uv_a STATIC ${uv_sources})

set_target_properties(uv_a PROPERTIES
    OUTPUT_NAME uv
)

target_include_directories(uv_a
    PUBLIC
        ${_uv_src}/include
    PRIVATE
        ${_uv_src}/src
)

target_compile_definitions(uv_a PRIVATE
    _GNU_SOURCE
    _POSIX_C_SOURCE=200112
    _FILE_OFFSET_BITS=64
    _LARGEFILE_SOURCE
)

if(CMAKE_C_COMPILER_ID MATCHES "GNU|Clang")
    target_compile_options(uv_a PRIVATE -Wno-unused-parameter)
endif()

target_link_libraries(uv_a PUBLIC pthread dl rt m)

target_sources(uv_a PUBLIC
    FILE_SET HEADERS
    BASE_DIRS ${_uv_src}/include
    FILES     ${uv_inc}
)

install(TARGETS uv_a
    ARCHIVE   DESTINATION lib
    FILE_SET HEADERS DESTINATION include
)