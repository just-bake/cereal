cmake_minimum_required(VERSION 3.6)

project(cereal LANGUAGES CXX VERSION 1.3.2)

# Set C++ standard
set(CMAKE_CXX_STANDARD 11)
set(CMAKE_CXX_STANDARD_REQUIRED ON)

# Thread safety options
set(CEREAL_THREAD_LIBS)
if(UNIX)
    option(THREAD_SAFE "Use mutexes to ensure thread safety" OFF)
    if(THREAD_SAFE)
        message(STATUS "Use mutexes")
        add_definitions(-DCEREAL_THREAD_SAFE=1)
        set(CEREAL_THREAD_LIBS pthread)
    endif()
endif()

# Compiler flags
if(MSVC)
    add_compile_options(/bigobj /W3 /WX)
else()
    add_compile_options(-Wall -Wextra -pedantic -Wshadow -Wold-style-cast)
    option(WITH_WERROR "Compile with '-Werror' C++ compiler flag" ON)
    if(WITH_WERROR)
        add_compile_options(-Werror)
    endif()

    option(CLANG_USE_LIBCPP "Use libc++ for clang compilation" OFF)
    if(APPLE OR CLANG_USE_LIBCPP)
        message(STATUS "Use libc++")
        add_compile_options(-stdlib=libc++)
        set(CMAKE_EXE_LINKER_FLAGS "${CMAKE_EXE_LINKER_FLAGS} -stdlib=libc++ -lc++abi")
    endif()
endif()

# Create the cereal library target
add_library(cereal INTERFACE)
add_library(cereal::cereal ALIAS cereal)

# Add include directories
target_include_directories(cereal INTERFACE
    $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}/include>
    $<INSTALL_INTERFACE:include>
)

# Add C++11 features if supported
if(NOT CMAKE_VERSION VERSION_LESS 3.8)
    target_compile_features(cereal INTERFACE cxx_std_11)
endif()
