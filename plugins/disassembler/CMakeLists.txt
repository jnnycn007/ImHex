cmake_minimum_required(VERSION 3.16)

include(ImHexPlugin)

if (NOT USE_SYSTEM_CAPSTONE)
    set(CAPSTONE_BUILD_STATIC_RUNTIME OFF CACHE BOOL "Disable shared library building")
    set(CAPSTONE_BUILD_SHARED OFF CACHE BOOL "Disable shared library building")
    set(CAPSTONE_BUILD_TESTS OFF CACHE BOOL "Disable tests")
    add_subdirectory(${THIRD_PARTY_LIBS_FOLDER}/capstone ${CMAKE_CURRENT_BINARY_DIR}/capstone EXCLUDE_FROM_ALL)
    set_target_properties(capstone PROPERTIES POSITION_INDEPENDENT_CODE ON)
    target_compile_options(capstone PRIVATE -Wno-unused-function)
    set(CAPSTONE_LIBRARIES "capstone")
    set(CAPSTONE_INCLUDE_DIRS ${THIRD_PARTY_LIBS_FOLDER}/capstone/include)
else()
    find_package(PkgConfig REQUIRED)
    pkg_search_module(CAPSTONE 4.0.2 REQUIRED capstone)
endif()

add_imhex_plugin(
    NAME
        disassembler
    SOURCES
        source/plugin_disassembler.cpp

        source/content/views/view_disassembler.cpp

        source/content/pl_visualizers/disassembler.cpp
    INCLUDES
        include
    LIBRARIES
        ${CAPSTONE_LIBRARIES}
        ui
)