cmake_minimum_required(VERSION 3.2)

#include("cmake/HunterGate.cmake")
#HunterGate(
#    URL "https://github.com/ruslo/hunter/archive/v0.23.34.tar.gz"
#    SHA1 "70287b1ffa810ee4e952052a9adff9b4856d0d54"
#)

project(Qwt VERSION 6.1.3)

find_package(Qt5 COMPONENTS Core Concurrent PrintSupport REQUIRED)
#qt5_use_modules(qwt Core Widgets Concurrent OpenGL PrintSupport)
list(APPEND QT_LIBS 
  Qt5::Core
  Qt5::PrintSupport
  Qt5::Concurrent
)

set(CMAKE_AUTOMOC ON) # For meta object compiler
#set(CMAKE_AUTORCC ON) # Resource files
#set(CMAKE_AUTOUIC ON) # UI files
#set(CMAKE_INCLUDE_CURRENT_DIR ON)

list(APPEND QWT_SOURCES 
 	qwt/src/qwt_abstract_scale_draw.cpp
  qwt/src/qwt_clipper.cpp
  qwt/src/qwt_color_map.cpp
  qwt/src/qwt_column_symbol.cpp
  qwt/src/qwt_date.cpp
  qwt/src/qwt_date_scale_draw.cpp
  qwt/src/qwt_date_scale_engine.cpp
  qwt/src/qwt_dyngrid_layout.cpp
  qwt/src/qwt_event_pattern.cpp
  qwt/src/qwt_graphic.cpp
  qwt/src/qwt_interval.cpp
  qwt/src/qwt_interval_symbol.cpp
  qwt/src/qwt_math.cpp
  qwt/src/qwt_magnifier.cpp
  qwt/src/qwt_null_paintdevice.cpp
  qwt/src/qwt_painter.cpp
  qwt/src/qwt_painter_command.cpp
  qwt/src/qwt_panner.cpp
  qwt/src/qwt_picker.cpp
  qwt/src/qwt_picker_machine.cpp
  qwt/src/qwt_pixel_matrix.cpp
  qwt/src/qwt_point_3d.cpp
  qwt/src/qwt_point_polar.cpp
  qwt/src/qwt_round_scale_draw.cpp
  qwt/src/qwt_scale_div.cpp
  qwt/src/qwt_scale_draw.cpp
  qwt/src/qwt_scale_map.cpp
  qwt/src/qwt_spline.cpp
  qwt/src/qwt_scale_engine.cpp
  qwt/src/qwt_symbol.cpp
  qwt/src/qwt_system_clock.cpp
  qwt/src/qwt_text_engine.cpp
  qwt/src/qwt_text_label.cpp
  qwt/src/qwt_text.cpp
  qwt/src/qwt_transform.cpp
  qwt/src/qwt_widget_overlay.cpp
)
include_directories(qwt/src)


# Start processing the options
option(LIBQWT_ENABLE_PLOT "Enable the basic plot widget" ON)
if(LIBQWT_ENABLE_PLOT)
	list(APPEND QWT_SOURCES
	  qwt/src/qwt_curve_fitter.cpp
    qwt/src/qwt_abstract_legend.cpp
    qwt/src/qwt_legend.cpp
    qwt/src/qwt_legend_data.cpp
    qwt/src/qwt_legend_label.cpp
    qwt/src/qwt_plot.cpp
    qwt/src/qwt_plot_renderer.cpp
    qwt/src/qwt_plot_xml.cpp
    qwt/src/qwt_plot_axis.cpp
    qwt/src/qwt_plot_curve.cpp
    qwt/src/qwt_plot_dict.cpp
    qwt/src/qwt_plot_directpainter.cpp
    qwt/src/qwt_plot_grid.cpp
    qwt/src/qwt_plot_histogram.cpp
    qwt/src/qwt_plot_item.cpp
    qwt/src/qwt_plot_abstract_barchart.cpp
    qwt/src/qwt_plot_barchart.cpp
    qwt/src/qwt_plot_multi_barchart.cpp
    qwt/src/qwt_plot_intervalcurve.cpp
    qwt/src/qwt_plot_zoneitem.cpp
    qwt/src/qwt_plot_tradingcurve.cpp
    qwt/src/qwt_plot_spectrogram.cpp
    qwt/src/qwt_plot_spectrocurve.cpp
    qwt/src/qwt_plot_scaleitem.cpp
    qwt/src/qwt_plot_legenditem.cpp
    qwt/src/qwt_plot_seriesitem.cpp
    qwt/src/qwt_plot_shapeitem.cpp
    qwt/src/qwt_plot_marker.cpp
    qwt/src/qwt_plot_textlabel.cpp
    qwt/src/qwt_plot_layout.cpp
    qwt/src/qwt_plot_canvas.cpp
    qwt/src/qwt_plot_panner.cpp
    qwt/src/qwt_plot_rasteritem.cpp
    qwt/src/qwt_plot_picker.cpp
    qwt/src/qwt_plot_zoomer.cpp
    qwt/src/qwt_plot_magnifier.cpp
    qwt/src/qwt_plot_rescaler.cpp
    qwt/src/qwt_point_mapper.cpp
    qwt/src/qwt_raster_data.cpp
    qwt/src/qwt_matrix_raster_data.cpp
    qwt/src/qwt_sampling_thread.cpp
    qwt/src/qwt_series_data.cpp
    qwt/src/qwt_point_data.cpp
    qwt/src/qwt_scale_widget.cpp
	)
else()
	add_definitions(-DNO_QWT_PLOT) 
endif()

option(LIBQWT_ENABLE_WIDGETS "Enable widgets like the knob and the thermometer" ON)
if(LIBQWT_ENABLE_WIDGETS)
  find_package(Qt5Widgets REQUIRED)
	list(APPEND QT_LIBS Qt5::Widgets)
  list(APPEND QWT_SOURCES 
    qwt/src/qwt_abstract_slider.cpp
    qwt/src/qwt_abstract_scale.cpp
    qwt/src/qwt_arrow_button.cpp
    qwt/src/qwt_analog_clock.cpp
    qwt/src/qwt_compass.cpp
    qwt/src/qwt_compass_rose.cpp
    qwt/src/qwt_counter.cpp
    qwt/src/qwt_dial.cpp
    qwt/src/qwt_dial_needle.cpp
    qwt/src/qwt_knob.cpp
    qwt/src/qwt_slider.cpp
    qwt/src/qwt_thermo.cpp
    qwt/src/qwt_wheel.cpp
  )
else()
	add_definitions(-DNO_QWT_WIDGETS)
endif()

option(LIBQWT_ENABLE_SVG "Enable SVG items" ON)
if(LIBQWT_ENABLE_SVG)
  find_package(Qt5Svg REQUIRED) # QUIET)
	list(APPEND QT_LIBS Qt5::Svg)
  list(APPEND QWT_SOURCES 
    qwt/src/qwt_plot_svgitem.cpp
  )
else()
	add_definitions(-DQWT_NO_SVG)
endif()

option(LIBQWT_ENABLE_OPENGL "Enable OpenGL elements" ON)
if(LIBQWT_ENABLE_OPENGL)
  find_package(Qt5OpenGL REQUIRED)
	list(APPEND QT_LIBS Qt5::OpenGL)
	list(APPEND QWT_SOURCES 
    qwt/src/qwt_plot_glcanvas.cpp
	)
else()
	add_definitions(-DQWT_NO_OPENGL)
endif()

option(LIBQWT_ENABLE_MATHML "Enable Text engine based on mathml" ON)
if(LIBQWT_ENABLE_MATHML)
  find_package(Qt5Xml REQUIRED)
	list(APPEND QT_LIBS Qt5::Xml)
	list(APPEND QWT_SOURCES 
    qwt/src/textengines/mathml/qwt_mathml_text_engine.cpp)
	)
  include_directories(qwt/src/textengines/mathml)
else()
	add_definitions(-DQWT_NO_MATHML)
endif()


# Create the target and manage linking and includes
if(QWT_BUILD_SHARED_LIBS)
  add_library(qwt SHARED ${QWT_SOURCES})
  if(WIN32)
	  target_compile_definitions(qwt -DQT_DLL -DQWT_DLL -DQWT_MAKEDLL)
  endif()
else()
  add_library(qwt STATIC ${QWT_SOURCES})
endif()
include_directories(qwt PUBLIC "$<BUILD_INTERFACE:${CMAKE_CURRENT_LIST_DIR}/qwt/src>")
target_link_libraries(qwt ${QT_LIBS})
set_property(TARGET qwt PROPERTY SOVERSION ${PROJECT_VERSION})

# Add the installation settings
include(GNUInstallDirs)
set(generated_dir "${CMAKE_CURRENT_BINARY_DIR}/generated")
set(config_install_dir "lib/cmake/${PROJECT_NAME}")
set(include_install_dir "include")
set(version_config "${generated_dir}/${PROJECT_NAME}ConfigVersion.cmake")
set(project_config "${generated_dir}/${PROJECT_NAME}Config.cmake")
set(targets_export_name "${PROJECT_NAME}Targets")
set(namespace "${PROJECT_NAME}::")

#include(CMakePackageConfigHelpers)
#write_basic_package_version_file(
#    "${version_config}" COMPATIBILITY SameMajorVersion
#)
#configure_package_config_file(
#  "cmake/config.cmake.in"
#  "${project_config}"
#  INSTALL_DESTINATION "${config_install_dir}"
#)

install(TARGETS qwt 
  EXPORT ${targets_export_name}
  LIBRARY DESTINATION lib
  ARCHIVE DESTINATION lib
  INCLUDES DESTINATION ${include_install_dir}
)
install(
  DIRECTORY qwt/src/
  DESTINATION ${include_install_dir}
  FILES_MATCHING PATTERN "*.h"
)

#install(
#  FILES "${project_config}" "${version_config}"
#  DESTINATION "${config_install_dir}"
#)

install(
  EXPORT "${targets_export_name}"
  NAMESPACE "${namespace}"
  DESTINATION "${config_install_dir}"
)
