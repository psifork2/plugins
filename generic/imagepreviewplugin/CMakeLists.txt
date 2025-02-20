unset(_HDRS)
unset(_UIS)
unset(_SRCS)
unset(_RSCS)
unset(PLUGIN)

set( PLUGIN imagepreviewplugin )
project(${PLUGIN} LANGUAGES CXX)
cmake_minimum_required(VERSION 3.1.0)
if(POLICY CMP0071)
    if(${CMAKE_VERSION} VERSION_LESS "3.10.0")
        cmake_policy(SET CMP0071 OLD)
    else()
        cmake_policy(SET CMP0071 NEW)
    endif()
endif()
if(POLICY CMP0074)
    cmake_policy(SET CMP0074 NEW)
endif()
set( CMAKE_AUTOMOC TRUE )

if("${CMAKE_BUILD_TYPE}" STREQUAL "Debug" OR ("${CMAKE_BUILD_TYPE}" STREQUAL "RelWithDebInfo"))
    option(IMGPREVIEW_SCROLL_DEBUG "Add -DSCROLL_DEBUG definition" OFF)
    option(IMGPREVIEW_DEBUG "Add -DIMGPREVIEW_DEBUG definition" OFF)
    if(IMGPREVIEW_SCROLL_DEBUG)
        add_definitions(-DSCROLL_DEBUG)
    endif()
    if(IMGPREVIEW_DEBUG)
        add_definitions(-DIMGPREVIEW_DEBUG)
    endif()
endif()

find_package( Qt5 COMPONENTS Core REQUIRED )

get_filename_component(ABS_PLUGINS_ROOT_DIR "${CMAKE_CURRENT_LIST_DIR}/../.." ABSOLUTE)
set(PLUGINS_ROOT_DIR "${ABS_PLUGINS_ROOT_DIR}" CACHE STRING "Plugins root path. Path where include directory placed")
set( CMAKE_MODULE_PATH
    ${CMAKE_MODULE_PATH}
    ${PLUGINS_ROOT_DIR}/cmake/modules
)
find_package(PsiPluginsApi REQUIRED)
include(${PsiPluginsApi_DIR}/variables.cmake)
include_directories(
    ${CMAKE_CURRENT_BINARY_DIR}
    ${CMAKE_CURRENT_LIST_DIR}
    ${PsiPluginsApi_INCLUDE_DIR}
)
if(NOT PLUGINS_INSTALL_PATH)
    set(PLUGINS_INSTALL_PATH "${CMAKE_INSTALL_PREFIX}/${PLUGINS_PATH}")
endif()

set( _SRCS
    ${PLUGIN}.cpp
    ScrollKeeper.cpp
    options.cpp
)

set( _HDRS
    ScrollKeeper.h
    options.h
)

set( _UIS
    options.ui
)

find_package(Qt5 COMPONENTS Widgets Xml Network REQUIRED)
set(QT_DEPLIBS
    Qt5::Widgets
    Qt5::Xml
    Qt5::Network
)
qt5_wrap_ui(UIS ${_UIS})
qt5_add_resources(RSCS ${_RSCS})

if(WIN32)
    set(LIB_TYPE "MODULE")
else()
    set(LIB_TYPE "SHARED")
endif()

add_library(
    ${PLUGIN}
    ${LIB_TYPE}
    ${_SRCS}
    ${RSCS}
    ${UIS}
)
target_link_libraries(
    ${PLUGIN}
    ${QT_DEPLIBS}
)

install(
    TARGETS
    ${PLUGIN}
    LIBRARY
    DESTINATION
    ${PLUGINS_INSTALL_PATH}
    RUNTIME DESTINATION
    ${PLUGINS_INSTALL_PATH}
)
