cmake_minimum_required(VERSION 3.10)
project(WebGLCppCalculator VERSION 1.0.0 LANGUAGES CXX)

set(CMAKE_CXX_STANDARD 17)
set(CMAKE_CXX_STANDARD_REQUIRED ON)

# Emscripten specific settings
if(EMSCRIPTEN)
    set(CMAKE_EXECUTABLE_SUFFIX ".js")
    
    # Link flags for WebGL and optimization
    set(CMAKE_EXE_LINKER_FLAGS "${CMAKE_EXE_LINKER_FLAGS} \
        -s USE_WEBGL2=1 \
        -s FULL_ES3=1 \
        -s ALLOW_MEMORY_GROWTH=1 \
        -s NO_EXIT_RUNTIME=1 \
        -s EXPORTED_FUNCTIONS='[\"_main\", \"_calculate\", \"_render3D\"]' \
        -s EXPORTED_RUNTIME_METHODS='[\"ccall\", \"cwrap\"]' \
        --preload-file assets@/assets \
        -O3")
endif()

# Include directories
include_directories(include)

# Source files
file(GLOB_RECURSE SOURCES 
    src/core/*.cpp
    src/graphics/*.cpp
    src/ui/*.cpp
    src/main.cpp
)

# Create executable
add_executable(${PROJECT_NAME} ${SOURCES})

# Copy assets after build
if(EMSCRIPTEN)
    add_custom_command(TARGET ${PROJECT_NAME} POST_BUILD
        COMMAND ${CMAKE_COMMAND} -E copy_if_different
        ${CMAKE_SOURCE_DIR}/index.html
        ${CMAKE_BINARY_DIR}/index.html
    )
endif()
