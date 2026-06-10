if(NOT DEFINED UV_LIB_MODE)
    set(UV_LIB_MODE "${ACLASS_LIB_MODE}")
endif()

set(uv_src
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

set(uv_inc
    include/uv.h
    include/uv/errno.h
    include/uv/threadpool.h
    include/uv/version.h
    include/uv/linux.h
    include/uv/unix.h
)

add_library(uv ${UV_LIB_MODE})

target_include_directories(uv PRIVATE
    ${CMAKE_CURRENT_SOURCE_DIR}/src
)

target_sources(uv PRIVATE
    ${uv_src}
)

target_sources(uv PUBLIC
    FILE_SET HEADERS
    BASE_DIRS ${CMAKE_CURRENT_SOURCE_DIR}/include
    FILES     ${uv_inc}
)

set_target_properties(uv PROPERTIES
    OUTPUT_NAME uv
)

target_compile_definitions(uv PRIVATE
    _GNU_SOURCE
    _POSIX_C_SOURCE=200112
    _FILE_OFFSET_BITS=64
    _LARGEFILE_SOURCE
)

if(CMAKE_C_COMPILER_ID MATCHES "GNU|Clang")
    target_compile_options(uv PRIVATE -Wno-unused-parameter)
endif()

target_link_libraries(uv PUBLIC pthread dl rt m)

install(TARGETS uv
    LIBRARY   DESTINATION lib
    ARCHIVE   DESTINATION lib
    FILE_SET HEADERS DESTINATION include
)