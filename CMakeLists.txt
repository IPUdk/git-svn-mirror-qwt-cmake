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
include_directories(${CMAKE_CURRENT_SOURCE_DIR}/qwt/classincludes ${CMAKE_CURRENT_SOURCE_DIR}/qwt/src ${CMAKE_CURRENT_SOURCE_DIR}/qwt/textengines ${CMAKE_CURRENT_SOURCE_DIR}/qwt/textengines/mathml)

list(REMOVE_ITEM QWT_MAIN_SRC "${CMAKE_CURRENT_SOURCE_DIR}/qwt/src/qwt_plot_svgitem.cpp")
add_definitions(-DQWT_NO_SVG)

if(FALSE)
  list(REMOVE_ITEM QWT_MAIN_SRC "${CMAKE_CURRENT_SOURCE_DIR}/qwt/src/qwt_plot_glcanvas.cpp")
  list(REMOVE_ITEM QWT_MAIN_SRC "${CMAKE_CURRENT_SOURCE_DIR}/qwt/src/qwt_plot_opengl_canvas.cpp")
  add_definitions(-DQWT_NO_OPENGL)
else()
  find_package(OpenGL REQUIRED)
  list(APPEND LINK_LIBS ${OPENGL_LIBRARIES})
  #target_include_directories(<your-target> PRIVATE ${OPENGL_INCLUDE_DIR})
  #target_link_libraries(<your-target> PRIVATE ${OPENGL_LIBRARIES})
  find_package(Qt5 COMPONENTS OpenGL REQUIRED)
  list(APPEND QT_MODULES OpenGL)
endif()

find_package(Qt5 COMPONENTS Widgets Xml REQUIRED)
list(APPEND QT_MODULES Widgets Xml)


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

if(WIN32)
  set_target_properties(qwt PROPERTIES DEBUG_POSTFIX "d")
endif()

qt5_use_modules(qwt ${QT_MODULES})
target_link_libraries(qwt ${QT_LIBRARIES} ${LINK_LIBS})

#include_directories(qwt PUBLIC "$<BUILD_INTERFACE:${CMAKE_CURRENT_LIST_DIR}/qwt/src>")
set_property(TARGET qwt PROPERTY SOVERSION ${PROJECT_VERSION})

export(TARGETS qwt FILE ${CMAKE_CURRENT_BINARY_DIR}/qwt-exports.cmake)

# Provide a macro that handles the library deployment and the include paths
macro(add_qwt_to_target param_TARGETNAME)
  add_dependencies(${param_TARGETNAME} qwt)
  target_link_libraries(${param_TARGETNAME} $<TARGET_LINKER_FILE:qwt>)
  target_include_directories(${param_TARGETNAME} PUBLIC $<TARGET_PROPERTY:qwt,INCLUDE_DIRECTORIES>)
  if(QWT_BUILD_SHARED_LIBS)
    add_custom_command(
      TARGET ${param_TARGETNAME}
      POST_BUILD
      COMMAND ${CMAKE_COMMAND} -E copy "$<TARGET_FILE:qwt>" "$<TARGET_FILE_DIR:${param_TARGETNAME}>"
      COMMENT "Deploying the qwt library"
    )
  endif(QWT_BUILD_SHARED_LIBS)
endmacro()