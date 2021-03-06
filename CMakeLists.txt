#  minimum required cmake version: 3.1.0
cmake_minimum_required(VERSION 3.1.0)
project(librealuvc LANGUAGES CXX C)

set(CMAKE_MODULE_PATH "${CMAKE_CURRENT_SOURCE_DIR}/CMake" CACHE PATH "cmake modules" FORCE)

# include librealsense general configuration
include(CMake/global_config.cmake)

config_cxx_flags()

# include os specific macros
# macro definition located at "CMake/global_config.cmake"
include(CMake/include_os.cmake)

# set os specific configuration flags
# macro definition located at "CMake/<OS>_config.cmake"
os_set_flags()

# set global configuration flags
# macro definition located at "CMake/global_config.cmake"
global_set_flags()

if(BUILD_SHARED_LIBS)
  message(STATUS "add_library(${LRS_TARGET} SHARED)")
  add_library(${LRS_TARGET} SHARED "")
endif()
if(BUILD_STATIC_LIBS)
  message(STATUS "add_library(${LRS_TARGET}_static STATIC)")
  add_library(${LRS_TARGET}_static STATIC "")
endif()

# add_OpenCV()
find_package(OpenCV REQUIRED)
set(DEPENDENCIES librealuvc ${OpenCV_LIBS})
include_directories(${OpenCV_INCLUDE_DIRS})

add_library(OpenCV SHARED IMPORTED)

# global project configuration
# macro definition located at "CMake/global_config.cmake"
global_target_config()

# configure the project according to OS specific requirments
# macro definition located at "CMake/<OS>_config.cmake"
os_target_config()

include(include/CMakeLists.txt)
include(src/CMakeLists.txt)
include(third-party/CMakeLists.txt)

# set library version
set_target_properties(${LRS_TARGET} PROPERTIES
  WINDOWS_EXPORT_ALL_SYMBOLS ON
  VERSION ${REALUVC_VERSION_STRING}
  SOVERSION ${REALUVC_VERSION_MAJOR}
)

add_subdirectory(wrappers)

if(BUILD_EXAMPLES)
  add_subdirectory(examples)
endif()

if(BUILD_UNIT_TESTS)
  add_subdirectory(unit-tests)
endif()

include(CMake/install_config.cmake)