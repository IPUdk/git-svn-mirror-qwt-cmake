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


# Implement some rules to handle platform specific settings
if (MSVC)
  find_package(Qt5 COMPONENTS Svg REQUIRED)
  list(APPEND QT_MODULES Svg)
else (MSVC)
  list(REMOVE_ITEM QWT_MAIN_SRC "${CMAKE_CURRENT_SOURCE_DIR}/qwt/src/qwt_plot_svgitem.cpp")
  add_definitions(-DQWT_NO_SVG)
endif (MSVC)

if (MSVC)
  if (MSVC_VERSION GREATER_EQUAL 1910) # VS 15.0 - 2017
    list(REMOVE_ITEM QWT_MAIN_SRC "${CMAKE_CURRENT_SOURCE_DIR}/qwt/src/qwt_plot_glcanvas.cpp")
    list(REMOVE_ITEM QWT_MAIN_SRC "${CMAKE_CURRENT_SOURCE_DIR}/qwt/src/qwt_plot_opengl_canvas.cpp")
    add_definitions(-DQWT_NO_OPENGL)
  else (MSVC_VERSION GREATER_EQUAL 1910)
    list(APPEND QT_MODULES OpenGL)
  endif()
else (MSVC)
  find_package(OpenGL REQUIRED)
  list(APPEND LINK_LIBS ${OPENGL_LIBRARIES})
  #target_include_directories(<your-target> PRIVATE ${OPENGL_INCLUDE_DIR})
  #target_link_libraries(<your-target> PRIVATE ${OPENGL_LIBRARIES})
  find_package(Qt5 COMPONENTS OpenGL REQUIRED)
  list(APPEND QT_MODULES OpenGL)
endif (MSVC)


find_package(Qt5 COMPONENTS Widgets Xml REQUIRED)
list(APPEND QT_MODULES Widgets Xml)


set(QWT_SOURCES ${QWT_MAIN_SRC})

# Create the targets and manage linking and includes
add_library(qwt_dll SHARED ${QWT_SOURCES})
if(WIN32)
  target_compile_definitions(qwt_dll PUBLIC QT_DLL QWT_DLL QWT_MAKEDLL)
endif()

add_library(qwt_lib STATIC ${QWT_SOURCES})
#if(WIN32)
#  target_compile_definitions(qwt_lib PUBLIC QT_DLL)
#endif()

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


foreach (tgt qwt;qwt_dll;qwt_lib)
  if(WIN32)
    #target_compile_definitions(${tgt} PUBLIC QT_DLL)
    set_target_properties(${tgt} PROPERTIES DEBUG_POSTFIX "d")
  endif()
  qt5_use_modules(${tgt} ${QT_MODULES})
  target_link_libraries(${tgt} ${LINK_LIBS})
  #include_directories(${tgt} PUBLIC "$<BUILD_INTERFACE:${CMAKE_CURRENT_LIST_DIR}/qwt/src>")
  set_property(TARGET ${tgt} PROPERTY SOVERSION ${PROJECT_VERSION})
endforeach()

export(TARGETS qwt_dll qwt_lib FILE ${CMAKE_CURRENT_BINARY_DIR}/qwt-exports.cmake)

# Provide a macro that handles the library deployment and the include paths
macro(add_qwt_dll_to_target param_TARGETNAME param_LIBNAME)
  add_dependencies(${param_TARGETNAME} ${param_LIBNAME})
  qt5_use_modules(${param_TARGETNAME} ${QT_MODULES})
  target_link_libraries(${param_TARGETNAME} $<TARGET_LINKER_FILE:${param_LIBNAME}>)
  target_include_directories(${param_TARGETNAME} PUBLIC $<TARGET_PROPERTY:${param_LIBNAME},INCLUDE_DIRECTORIES>)
  add_custom_command(
    TARGET ${param_TARGETNAME}
    POST_BUILD
    COMMAND ${CMAKE_COMMAND} -E copy "$<TARGET_FILE:${param_LIBNAME}>" "$<TARGET_FILE_DIR:${param_TARGETNAME}>"
    COMMENT "Deploying the ${param_LIBNAME} library"
  )
  #install(TARGETS ${param_LIBNAME})
  target_link_libraries(${param_TARGETNAME} ${LINK_LIBS})
endmacro()

macro(add_qwt_lib_to_target param_TARGETNAME param_LIBNAME)
  add_dependencies(${param_TARGETNAME} ${param_LIBNAME})
  qt5_use_modules(${param_TARGETNAME} ${QT_MODULES})
  target_link_libraries(${param_TARGETNAME} $<TARGET_LINKER_FILE:${param_LIBNAME}>)
  target_include_directories(${param_TARGETNAME} PUBLIC $<TARGET_PROPERTY:${param_LIBNAME},INCLUDE_DIRECTORIES>)
endmacro()

add_executable(simpleplot "${CMAKE_CURRENT_SOURCE_DIR}/qwt/examples/simpleplot/simpleplot.cpp")
add_qwt_lib_to_target(simpleplot qwt_lib)
