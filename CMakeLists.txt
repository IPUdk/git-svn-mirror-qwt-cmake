cmake_minimum_required(VERSION 3.2)

project(Qwt VERSION 6.1.3)

find_package(Qt5 COMPONENTS Core Concurrent PrintSupport REQUIRED)
#qt5_use_modules(qwt Core Widgets Concurrent OpenGL PrintSupport)
list(APPEND QT_MODULES
  Core
  PrintSupport
  Concurrent
)

set(CMAKE_AUTOMOC ON) # For meta object compiler
set(CMAKE_AUTORCC ON) # Resource files
set(CMAKE_AUTOUIC ON) # UI files
#set(CMAKE_INCLUDE_CURRENT_DIR ON)

file(GLOB QWT_MAIN_SRC ${CMAKE_CURRENT_SOURCE_DIR}/qwt/src/*.cpp ${CMAKE_CURRENT_SOURCE_DIR}/qwt/textengines/*.cpp ${CMAKE_CURRENT_SOURCE_DIR}/qwt/textengines/mathml/*.cpp)
include_directories(${CMAKE_CURRENT_SOURCE_DIR}/qwt/src ${CMAKE_CURRENT_SOURCE_DIR}/qwt/textengines ${CMAKE_CURRENT_SOURCE_DIR}/qwt/textengines/mathml)

list(REMOVE_ITEM QWT_MAIN_SRC "${CMAKE_CURRENT_SOURCE_DIR}/qwt/src/qwt_plot_svgitem.cpp")
add_definitions(-DQWT_NO_SVG)

if(FALSE)
  list(REMOVE_ITEM QWT_MAIN_SRC "${CMAKE_CURRENT_SOURCE_DIR}/qwt/src/qwt_plot_glcanvas.cpp")
  list(REMOVE_ITEM QWT_MAIN_SRC "${CMAKE_CURRENT_SOURCE_DIR}/qwt/src/qwt_plot_opengl_canvas.cpp")
  add_definitions(-DQWT_NO_OPENGL)
else()
  if(MSVC)
    list(APPEND LINK_LIBS opengl32 glu32)
  endif(MSVC)
endif()



find_package(Qt5 COMPONENTS Widgets Xml OpenGL REQUIRED)
list(APPEND QT_MODULES Widgets Xml OpenGL)


set(QWT_SOURCES ${QWT_MAIN_SRC})

#cmake_policy(SET CMP0057 NEW)
#foreach(src_file ${QWT_MAIN_SRC})
#  if ("${src_file}" IN_LIST "${QWT_SOURCES}")
#    # Success
#  else()
#    message(STATUS "Missing from sources list: ${src_file}")
#  endif()
#endforeach()


# Create the target and manage linking and includes
option(QWT_BUILD_SHARED_LIBS "Build a DLL and use dynamic Qt libraries" ON)
if(QWT_BUILD_SHARED_LIBS)
  add_library(qwt SHARED ${QWT_SOURCES})
  if(WIN32)
    target_compile_definitions(qwt PUBLIC QT_DLL QWT_DLL QWT_MAKEDLL)
  endif()
else()
  add_library(qwt STATIC ${QWT_SOURCES})
  if(WIN32)
    target_compile_definitions(qwt PUBLIC QT_DLL)
  endif()
endif()

qt5_use_modules(qwt ${QT_MODULES})
target_link_libraries(qwt ${QT_LIBRARIES} ${LINK_LIBS})

#include_directories(qwt PUBLIC "$<BUILD_INTERFACE:${CMAKE_CURRENT_LIST_DIR}/qwt/src>")
set_property(TARGET qwt PROPERTY SOVERSION ${PROJECT_VERSION})

