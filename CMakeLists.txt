cmake_minimum_required(VERSION 3.20)

# Before everything else, set the toolchain file (if needed)
if (WIN32)
    set(CMAKE_TOOLCHAIN_FILE "$ENV{VCPKG_ROOT}/scripts/buildsystems/vcpkg.cmake" CACHE STRING "")
endif()

##########################
# Default compile options
##########################

if (NOT DEFINED SKIP_PKG_RESOURCES)
    set (SKIP_PKG_RESOURCES false)
endif()

##########################

set( CMAKE_RUNTIME_OUTPUT_DIRECTORY ${CMAKE_CURRENT_SOURCE_DIR}/bin )
set( CMAKE_LIBRARY_OUTPUT_DIRECTORY ${CMAKE_CURRENT_SOURCE_DIR}/lib )
set( CMAKE_ARCHIVE_OUTPUT_DIRECTORY ${CMAKE_CURRENT_SOURCE_DIR}/lib)

if (WIN32)
    add_compile_options(/bigobj)
endif ()

##########################!!!!!!!!!!!!!!!!!
## SET YOUR PROJECT NAME HERE!!!
##########################!!!!!!!!!!!!!!!!!
project(le3-empty-template)

include(CTest)
set(CMAKE_CXX_STANDARD 20)
set(CMAKE_CXX_STANDARD_REQUIRED TRUE)

set(LE3_WITH_DEMOS false)
set(LE3_WITH_EDITOR true)
set(LE3_WITH_GAME true)


############################################################
#@ Setup OpenMP & CGAL
############################################################
# if(APPLE)
#     set(CMAKE_C_COMPILER clang)
#     set(CMAKE_CXX_COMPILER clang++)

#     if(CMAKE_C_COMPILER_ID MATCHES "Clang\$")
#         set(OpenMP_C_FLAGS "-Xpreprocessor -fopenmp")
#         set(OpenMP_C_LIB_NAMES "omp")
#         set(OpenMP_omp_LIBRARY omp)
#     endif()

#     if(CMAKE_CXX_COMPILER_ID MATCHES "Clang\$")
#         set(OpenMP_CXX_FLAGS "-Xpreprocessor -fopenmp")
#         set(OpenMP_CXX_LIB_NAMES "omp")
#         set(OpenMP_omp_LIBRARY omp)
#     endif()
# endif()

set(CGAL_DO_NOT_WARN_ABOUT_CMAKE_BUILD_TYPE TRUE)
find_package(fmt REQUIRED)
find_package(Boost COMPONENTS container REQUIRED)
find_package(GLEW REQUIRED)
find_package(Lua REQUIRED)
find_package(glm CONFIG REQUIRED)
find_package(assimp REQUIRED)
find_package(CGAL REQUIRED)
# find_package(OpenMP REQUIRED)
find_package(nlohmann_json REQUIRED)
include_directories(${GLEW_INCLUDE_DIRS} ${LUA_INCLUDE_DIR})

if(APPLE)
    include_directories("/opt/homebrew/opt/libomp/include")
    link_directories("/opt/homebrew/opt/libomp/lib")
endif()

include_directories(${CMAKE_SOURCE_DIR}/third-party/LightEngine3/le3/include)
include_directories(${CMAKE_SOURCE_DIR}/third-party/LightEngine3/third-party/native_message_box/include)
include_directories(${CMAKE_SOURCE_DIR}/third-party/LightEngine3/third-party/imgui-filebrowser)
include_directories(${CMAKE_SOURCE_DIR}/third-party/LightEngine3/third-party/imgui)
include_directories(${PROJECT_SOURCE_DIR}/include)


add_subdirectory(third-party/LightEngine3)

if (NOT DEFINED LUA_LIBRARIES)
    set(LUA_LIBRARIES lua::lua)
endif()

# Copy the resources directory to the build
if (NOT SKIP_PKG_RESOURCES)
if (WIN32)
else()
    add_custom_target(
        pkg-resources-template ALL
        COMMAND 
            ${CMAKE_SOURCE_DIR}/third-party/LightEngine3/bin/pkg-resources ${CMAKE_CURRENT_SOURCE_DIR}/resources ${CMAKE_SOURCE_DIR}/bin

        DEPENDS ${CMAKE_SOURCE_DIR}/third-party/LightEngine3/bin/pkg-resources
    )
endif()
else()
    message(WARNING "Skipping pkg resources")
endif()
