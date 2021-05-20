cmake_minimum_required(VERSION 3.15)
cmake_policy(SET CMP0091 NEW)
project(rnnoise)

add_definitions(-DCOMPILE_OPUS)

# Ignore CRT warnings
add_definitions(-D_CRT_SECURE_NO_WARNINGS)

# Add math defines
add_definitions(-D_USE_MATH_DEFINES)

# Get source files
file(GLOB SOURCES "src/*.c" "src/*.h" "include/*.h")

# Build rnnoise
add_definitions(-DRNNOISE_BUILD)

# Compile the library
add_library(rnnoise ${SOURCES})

set_property(TARGET rnnoise PROPERTY
  MSVC_RUNTIME_LIBRARY "MultiThreaded$<$<CONFIG:Debug>:Debug>")

# Build DLL if needed
if(BUILD_SHARED_LIBS)
  if(WIN32)
    target_compile_definitions(rnnoise PRIVATE DLL_EXPORT)
  else()
    include(CheckCCompilerFlag)
    check_c_compiler_flag(-fvisibility=hidden COMPILER_HAS_HIDDEN_VISIBILITY)
    if(COMPILER_HAS_HIDDEN_VISIBILITY)
      set_target_properties(rnnoise PROPERTIES C_VISIBILITY_PRESET hidden)
    endif()
  endif()
endif()

# Include dirs
target_include_directories(rnnoise PUBLIC
        $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}/include>
        $<INSTALL_INTERFACE:include>
        PRIVATE src)