cmake_minimum_required(VERSION 3.24)

if(NOT "${METAMODULE_SDK_DIR}" STREQUAL "")
	message("METAMODULE_SDK_DIR set by CMake variable ${METAMODULE_SDK_DIR}")
elseif (DEFINED ENV{METAMODULE_SDK_DIR})
    set(METAMODULE_SDK_DIR "$ENV{METAMODULE_SDK_DIR}")
	message("METAMODULE_SDK_DIR set by environment variable ${METAMODULE_SDK_DIR}")
else()
    set(METAMODULE_SDK_DIR "${CMAKE_CURRENT_LIST_DIR}/../metamodule-plugin-sdk")
	message("METAMODULE_SDK_DIR set to default: ${METAMODULE_SDK_DIR}")
endif()
include(${METAMODULE_SDK_DIR}/plugin.cmake)

project(AmalgamatedHarmonics VERSION 1.0.0 DESCRIPTION "AmalgamatedHarmonics for Rack" LANGUAGES C CXX)

# Set C++17 standard
set(CMAKE_CXX_STANDARD 20)
set(CMAKE_CXX_STANDARD_REQUIRED ON)
set(CMAKE_CXX_EXTENSIONS OFF)

# Add option for virtual build
option(BUILD_VIRTUAL "Build for virtual environment" ON)

# Define source directories
set(SOURCE_DIR ${CMAKE_CURRENT_SOURCE_DIR}/AmalgamatedHarmonics)

# Main library target
add_library(AmalgamatedHarmonics STATIC)

# Compiler definitions
target_compile_definitions(AmalgamatedHarmonics PRIVATE 
    __V1x 
    __PLUGIN
    $<$<BOOL:${BUILD_VIRTUAL}>:BUILD_VIRTUAL>
    _MB_CAPABLE=1
)

# Compiler options
target_compile_options(AmalgamatedHarmonics PRIVATE
    -fpermissive
    -fdata-sections
    -ffunction-sections
    -fno-exceptions
    -fno-threadsafe-statics
    -fno-use-cxa-atexit
    $<$<CXX_COMPILER_ID:GNU>:-fvisibility=hidden>
    $<$<CXX_COMPILER_ID:GNU>:-fvisibility-inlines-hidden>
)

# Linker options
target_link_options(AmalgamatedHarmonics PRIVATE
    -Wl,--gc-sections
)

# Source files
set(AMALGAMATEDHARMONICS_SOURCES
    "${SOURCE_DIR}/src/AH.cpp"
    "${SOURCE_DIR}/src/AHCommon.cpp"
    "${SOURCE_DIR}/src/Arp31c.cpp"
    "${SOURCE_DIR}/src/Arp32p.cpp"
    "${SOURCE_DIR}/src/ArpeggiatorMkII.cpp"
    "${SOURCE_DIR}/src/Bombe.cpp"
    "${SOURCE_DIR}/src/Chord.cpp"
    "${SOURCE_DIR}/src/Circle.cpp"
    "${SOURCE_DIR}/src/Galaxy.cpp"
    "${SOURCE_DIR}/src/Generative.cpp"
    "${SOURCE_DIR}/src/Imp.cpp"
    "${SOURCE_DIR}/src/Imperfect2.cpp"
    # "${SOURCE_DIR}/src/MuxDeMux.cpp"
    # "${SOURCE_DIR}/src/PolyProbe.cpp"
    # "${SOURCE_DIR}/src/PolyScope.cpp"
    # "${SOURCE_DIR}/src/PolyUtils.cpp"
    # "${SOURCE_DIR}/src/PolyVolt.cpp"
    "${SOURCE_DIR}/src/Progress.cpp"
    "${SOURCE_DIR}/src/Progress2.cpp"
    "${SOURCE_DIR}/src/ProgressState.cpp"
    "${SOURCE_DIR}/src/Ruckus.cpp"
    "${SOURCE_DIR}/src/SLN.cpp"
    "${SOURCE_DIR}/src/ScaleQuantiser.cpp"
    "${SOURCE_DIR}/src/ScaleQuantizerMkII.cpp"
)

target_sources(AmalgamatedHarmonics PRIVATE
    ${AMALGAMATEDHARMONICS_SOURCES}
)

# Include directories
target_include_directories(AmalgamatedHarmonics PRIVATE 
    ${SOURCE_DIR}/src
)

# Create the plugin
create_plugin(
    SOURCE_LIB      AmalgamatedHarmonics
    PLUGIN_NAME     AmalgamatedHarmonics
    PLUGIN_JSON     ${SOURCE_DIR}/plugin.json
    SOURCE_ASSETS   ${CMAKE_CURRENT_LIST_DIR}/assets
    DESTINATION     ${CMAKE_CURRENT_LIST_DIR}/metamodule-plugins
) 