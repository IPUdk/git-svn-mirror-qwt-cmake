cmake_minimum_required(VERSION 2.8)
project(Qwt)

#QWT_BUILD_SHARED_LIBS=ON
#if(WIN32)
#	add_definitions(-DQT_DLL -DQWT_DLL -DQWT_MAKEDLL)
#endif()

find_package(Qt5 COMPONENTS Core Widgets Concurrent OpenGL PrintSupport REQUIRED)

set(CMAKE_AUTOMOC ON) # For meta object compiler
set(CMAKE_AUTORCC ON) # Resource files
set(CMAKE_AUTOUIC ON) # UI files

file(GLOB QWT_MAIN_SRC src/*.cpp)
file(GLOB QWT_MAIN_HDR src/*.h)
include_directories(${CMAKE_CURRENT_SOURCE_DIR}/src)

file(GLOB QWT_NGNS_SRC textengines/*.cpp)
file(GLOB QWT_NGNS_HDR textengines/*.h)
include_directories(${CMAKE_CURRENT_SOURCE_DIR}/textengines)

if(QWT_BUILD_SHARED_LIBS)
  add_library(qwt SHARED ${QWT_MAIN_SRC} ${QWT_NGNS_SRC})
else()
  add_library(qwt STATIC ${QWT_MAIN_SRC} ${QWT_NGNS_SRC})
endif()

if(WIN32)
  set_target_properties(qwt PROPERTIES DEBUG_POSTFIX "d")
  set(LINK_CRT_AS_DLL ON)
  foreach (flag_var
    CMAKE_C_FLAGS CMAKE_C_FLAGS_DEBUG CMAKE_C_FLAGS_RELEASE
    CMAKE_CXX_FLAGS CMAKE_CXX_FLAGS_DEBUG CMAKE_CXX_FLAGS_RELEASE )
    if (NOT LINK_CRT_AS_DLL)
      string(REPLACE "/MD" "/MT" ${flag_var} "${${flag_var}}")
    else()
      string(REPLACE "/MT" "/MD" ${flag_var} "${${flag_var}}")
    endif()
  endforeach()
endif()

qt5_use_modules(qwt Core Widgets Concurrent OpenGL Svg PrintSupport)
target_link_libraries(qwt ${QT_LIBRARIES})

install(TARGETS qwt
  EXPORT qwt-exports
  RUNTIME DESTINATION lib
  LIBRARY DESTINATION lib
  ARCHIVE DESTINATION lib
  )

install(FILES ${QWT_MAIN_HDR} DESTINATION include)
install(FILES ${QWT_NGNS_HDR} DESTINATION include)

install(EXPORT qwt-exports DESTINATION export)
