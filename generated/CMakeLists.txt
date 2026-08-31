cmake_minimum_required(VERSION 3.20)
project(gbarecomp_generated CXX)

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

add_library(gbarecomp_game STATIC ${GBARECOMP_GENERATED})
target_include_directories(gbarecomp_game PRIVATE
    "${CMAKE_CURRENT_SOURCE_DIR}/framework/include"
    "${CMAKE_CURRENT_SOURCE_DIR}/generated"
)
set_target_properties(gbarecomp_game PROPERTIES
    CXX_STANDARD 20
    CXX_STANDARD_REQUIRED YES
)
