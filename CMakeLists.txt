cmake_minimum_required(VERSION 3.20)
project(YGORecomp CXX)

set(CMAKE_CXX_STANDARD 20)
set(CMAKE_CXX_STANDARD_REQUIRED ON)

set(GBARECOMP_ROOT "${CMAKE_CURRENT_SOURCE_DIR}/../Projects/gbarecomp"
    CACHE PATH "Path to the gbarecomp runtime checkout")
set(YGO_BIOS_PATH "" CACHE FILEPATH
    "User-provided GBA BIOS to stage beside YGORecomp.exe")

set(YGO_SDL2_ROOT "${CMAKE_CURRENT_SOURCE_DIR}/../MyGameRecomp/third_party/SDL2/SDL2-2.32.10/x86_64-w64-mingw32"
    CACHE PATH "Local SDL2 MinGW package")
if(EXISTS "${YGO_SDL2_ROOT}/include/SDL2/SDL.h")
    set(GBARECOMP_MINGW_PREFIX_UNIX "${YGO_SDL2_ROOT}"
        CACHE PATH "SDL2 package root used by gbarecomp" FORCE)
endif()
set(YGO_TOMLPP_INCLUDE_DIR
    "${CMAKE_CURRENT_SOURCE_DIR}/../tomlplusplus/include/toml++")
if(EXISTS "${YGO_TOMLPP_INCLUDE_DIR}/toml.hpp")
    set(GBARECOMP_TOMLPP_INCLUDE_DIR "${YGO_TOMLPP_INCLUDE_DIR}"
        CACHE PATH "toml++ include directory used by gbarecomp" FORCE)
endif()

add_subdirectory("${GBARECOMP_ROOT}" gbarecomp_build EXCLUDE_FROM_ALL)

if(CMAKE_CXX_COMPILER)
    get_filename_component(YGO_COMPILER_BIN
        "${CMAKE_CXX_COMPILER}" DIRECTORY)
    set(YGO_MINGW_RUNTIME_BIN "C:/msys64/mingw64/bin")
    if(NOT EXISTS "${YGO_MINGW_RUNTIME_BIN}/libwinpthread-1.dll")
        set(YGO_MINGW_RUNTIME_BIN "${YGO_COMPILER_BIN}")
    endif()
    set(GBARECOMP_MINGW_RUNTIME_BIN "${YGO_MINGW_RUNTIME_BIN}"
        CACHE PATH "Compiler directory containing runtime DLLs" FORCE)
endif()

file(GLOB GBARECOMP_SHARDS CONFIGURE_DEPENDS
    "${CMAKE_CURRENT_SOURCE_DIR}/generated/recompiled_*.cpp"
)
set(GBARECOMP_GENERATED ${GBARECOMP_SHARDS}
    "${CMAKE_CURRENT_SOURCE_DIR}/generated/dispatch_table.cpp"
)
if(EXISTS "${CMAKE_CURRENT_SOURCE_DIR}/generated/symbol_map.cpp")
    list(APPEND GBARECOMP_GENERATED
        "${CMAKE_CURRENT_SOURCE_DIR}/generated/symbol_map.cpp")
endif()

add_executable(YGORecomp
    main.cpp
    ${GBARECOMP_GENERATED}
)
target_include_directories(YGORecomp PRIVATE
    "${CMAKE_CURRENT_SOURCE_DIR}/framework/include"
    "${CMAKE_CURRENT_SOURCE_DIR}/generated"
    "${GBARECOMP_ROOT}/src/armv4t"
    "${GBARECOMP_ROOT}/src/gba"
    "${GBARECOMP_ROOT}/src/runtime"
    "${GBARECOMP_ROOT}/src/debug"
)
target_link_libraries(YGORecomp PRIVATE
    gbarecomp_runtime
    gbarecomp_debug
    gbarecomp_gba
    gbarecomp_armv4t
)

if(MSVC)
    target_link_options(YGORecomp PRIVATE /STACK:16777216)
elseif(CMAKE_CXX_COMPILER_ID MATCHES "Clang")
    target_link_options(YGORecomp PRIVATE -Wl,/STACK:16777216)
elseif(MINGW OR WIN32 OR CMAKE_SYSTEM_NAME STREQUAL "MSYS")
    target_link_options(YGORecomp PRIVATE -Wl,--stack,16777216)
endif()

gbarecomp_stage_mingw_runtime(YGORecomp)

if(WIN32 AND YGO_COMPILER_BIN)
    foreach(ygo_runtime_dll IN ITEMS libc++.dll libunwind.dll)
        if(EXISTS "${YGO_COMPILER_BIN}/${ygo_runtime_dll}")
            add_custom_command(TARGET YGORecomp POST_BUILD
                COMMAND "${CMAKE_COMMAND}" -E copy_if_different
                    "${YGO_COMPILER_BIN}/${ygo_runtime_dll}"
                    "$<TARGET_FILE_DIR:YGORecomp>/${ygo_runtime_dll}")
        endif()
    endforeach()
endif()

if(WIN32 AND EXISTS "${YGO_MINGW_RUNTIME_BIN}")
    foreach(ygo_gcc_dll IN ITEMS
            libgmp-10.dll libisl-23.dll libmpc-3.dll libmpfr-6.dll
            libzstd.dll zlib1.dll libintl-8.dll libiconv-2.dll)
        if(EXISTS "${YGO_MINGW_RUNTIME_BIN}/${ygo_gcc_dll}")
            add_custom_command(TARGET YGORecomp POST_BUILD
                COMMAND "${CMAKE_COMMAND}" -E copy_if_different
                    "${YGO_MINGW_RUNTIME_BIN}/${ygo_gcc_dll}"
                    "$<TARGET_FILE_DIR:YGORecomp>/${ygo_gcc_dll}")
        endif()
    endforeach()
endif()

if(WIN32 AND EXISTS "${YGO_SDL2_ROOT}/bin/SDL2.dll")
    add_custom_command(TARGET YGORecomp POST_BUILD
        COMMAND "${CMAKE_COMMAND}" -E copy_if_different
            "${YGO_SDL2_ROOT}/bin/SDL2.dll"
            "$<TARGET_FILE_DIR:YGORecomp>/SDL2.dll")
endif()

add_custom_command(TARGET YGORecomp POST_BUILD
    COMMAND "${CMAKE_COMMAND}" -E copy_if_different
        "${CMAKE_CURRENT_SOURCE_DIR}/game.toml"
        "$<TARGET_FILE_DIR:YGORecomp>/game.toml")

if(WIN32 AND EXISTS "${YGO_BIOS_PATH}")
    add_custom_command(TARGET YGORecomp POST_BUILD
        COMMAND "${CMAKE_COMMAND}" -E copy_if_different
            "${YGO_BIOS_PATH}"
            "$<TARGET_FILE_DIR:YGORecomp>/gba_bios.bin")
endif()
