# License Agreement (3-clause BSD License)
# Copyright (c) 2015, Klaus Haag, all rights reserved.
# Third party copyrights and patents are property of their respective owners.
#
# Redistribution and use in source and binary forms, with or without modification,
# are permitted provided that the following conditions are met:
#
# * Redistributions of source code must retain the above copyright notice,
#   this list of conditions and the following disclaimer.
#
# * Redistributions in binary form must reproduce the above copyright notice,
#   this list of conditions and the following disclaimer in the documentation
#   and/or other materials provided with the distribution.
#
# * Neither the names of the copyright holders nor the names of the contributors
#   may be used to endorse or promote products derived from this software
#   without specific prior written permission.
#
# This software is provided by the copyright holders and contributors "as is" and
# any express or implied warranties, including, but not limited to, the implied
# warranties of merchantability and fitness for a particular purpose are disclaimed.
# In no event shall copyright holders or contributors be liable for any direct,
# indirect, incidental, special, exemplary, or consequential damages
# (including, but not limited to, procurement of substitute goods or services;
# loss of use, data, or profits; or business interruption) however caused
# and on any theory of liability, whether in contract, strict liability,
# or tort (including negligence or otherwise) arising in any way out of
# the use of this software, even if advised of the possibility of such damage.

project(KCF)

cmake_minimum_required(VERSION 3.1.0)

# option to enable OpenMP; only relevant for the KCF version with the
# VOT scale estimation
option(WITH_OPENMP "Enable OpenMP" OFF)

if(WITH_OPENMP)
    find_package(OpenMP REQUIRED)

    set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} ${OpenMP_CXX_FLAGS}")
    set(CMAKE_EXE_LINKER_FLAGS "${CMAKE_EXE_LINKER_FLAGS} ${OpenMP_EXE_LINKER_FLAGS}")
endif(WITH_OPENMP)

# add c++11 support
if(CMAKE_COMPILER_IS_GNUCC)
    ADD_DEFINITIONS ( -std=c++11 )
endif(CMAKE_COMPILER_IS_GNUCC)

# --------------------------------------------------
set(CMAKE_RUNTIME_OUTPUT_DIRECTORY ${KCF_BINARY_DIR}/bin)
set(CMAKE_ARCHIVE_OUTPUT_DIRECTORY ${KCF_BINARY_DIR}/lib)
set(CMAKE_LIBRARY_OUTPUT_DIRECTORY ${KCF_BINARY_DIR}/lib)

# --------------------------------------------------
# switch searching path to host, not NDK or arm gnu
# If set to NEVER, then the roots in CMAKE_FIND_ROOT_PATH will be ignored
# and only the host system root will be used
# --------------------------------------------------
if(ANDROID_NDK OR ARM_LINUX_SYSROOT)
set( CMAKE_FIND_ROOT_PATH_MODE_PROGRAM NEVER )
set( CMAKE_FIND_ROOT_PATH_MODE_LIBRARY NEVER )
set( CMAKE_FIND_ROOT_PATH_MODE_INCLUDE NEVER )
endif()

# add OpenCV
#set(OPENCV_DIR_HINT "")

#if(WIN32)
#    get_filename_component(OPENCV_DIR_PLATFORM $ENV{OPENCV_DIR} DIRECTORY)
#    get_filename_component(OPENCV_DIR_HINT ${OPENCV_DIR_PLATFORM} DIRECTORY)
#endif(WIN32)

#set(OpenCV_STATIC OFF)
#find_package(OpenCV REQUIRED HINTS ${OPENCV_DIR_HINT})

#
find_package(OpenMP)
if(OPENMP_FOUND)
	set(CMAKE_C_FLAGS "${CMAKE_C_FLAGS} ${OpenMP_C_FLAGS}")
	set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} ${OpenMP_CXX_FLAGS}")
	add_definitions(-DENABLE_OPENMP)
endif()

if(ANDROID_NDK OR ARM_LINUX_SYSROOT)
	#openCV 3.2.0 SDK by "make install"
	set(OpenCV_DIR $ENV{OPENCV_OUT}/sdk/native/jni)
	include( "${OpenCV_DIR}/OpenCVConfig.cmake" )
	message("++++OpenCV_DIR:${OpenCV_DIR}")
	FIND_PACKAGE( OpenCV REQUIRED)
	if(OpenCV_FOUND)
		message("found")
		message( "OpenCV_INCLUDE_DIRS:${OpenCV_INCLUDE_DIRS}" )
		message( "OpenCV_INCLUDES:${OpenCV_INCLUDES}")
		message( "OpenCV_LIBRARIES:${OpenCV_LIBRARIES}")
		message( "OpenCV_LIBS:${OpenCV_LIBS}")
		message( "OpenCV_DEFINITIONS:${OpenCV_DEFINITIONS}")
	else()
		message(FATAL_ERROR "openCV not foud")
	endif()

#	set(OpenCV_LIBS opencv_calib3d
#	opencv_highgui
#	opencv_objdetect
#	opencv_superres
#	opencv_core
#	opencv_imgcodecs
#	opencv_photo
#	opencv_videoio
#	opencv_features2d
#	opencv_imgproc
#	opencv_shape
#	opencv_video
#	opencv_flann
#	opencv_ml
#	opencv_stitching
#	opencv_videostab)

else()
	#QT5
	# Tell CMake to run moc when necessary:
	set(CMAKE_AUTOMOC ON)
	# As moc files are generated in the binary dir, tell CMake
	# to always look for includes there:
	set(CMAKE_INCLUDE_CURRENT_DIR ON)

	# Widgets finds its own dependencies (QtGui and QtCore).
	find_package(Qt5Widgets)
	#  find_package(Qt5Declarative)

	# The Qt5Widgets_INCLUDES also includes the include directories for
	# dependencies QtCore and QtGui
	include_directories(${Qt5Widgets_INCLUDES})

	# We need add -DQT_WIDGETS_LIB when using QtWidgets in Qt 5.
	add_definitions(${Qt5Widgets_DEFINITIONS})

	#openCV
	#set(OpenCV_DIR $ENV{OPENCV_OUT})
	#set(OpenCV_DIR "/home/thomas/opencv/build_2.4.13/debug")
	#set(OpenCV_DIR "/home/thomas/build/bcv-x86.gl/out/opencv/2.4.x-static/x86_64")
	set(OpenCV_DIR "/home/thomas/opencv/build_3.2.0/debug")
	message("++++OpenCV_DIR:${OpenCV_DIR}")
	FIND_PACKAGE( OpenCV REQUIRED)
	if(OpenCV_FOUND)
		message("found")
		message( "OpenCV_INCLUDE_DIRS:${OpenCV_INCLUDE_DIRS}" )
		message( "OpenCV_INCLUDES:${OpenCV_INCLUDES}")
		message( "OpenCV_LIBRARIES:${OpenCV_LIBRARIES}")
		message( "OpenCV_LIBS:${OpenCV_LIBS}")
		message( "OpenCV_DEFINITIONS:${OpenCV_DEFINITIONS}")
	else()
		message(FATAL_ERROR "openCV not foud")
	endif()
endif()

message("*** OpenCV_LIBS:${OpenCV_LIBS}")
message("*** OpenCV_LIBRARIES:${OpenCV_LIBRARIES}")

#FIND_PACKAGE( OpenCV REQUIRED )
link_directories ( ${OpenCV_LIB_DIR} )
#MESSAGE(STATUS "OpenCV_LIB_DIR: ${OpenCV_LIB_DIR}")

# add 3rdparty source
set(CF_TCLAP_DIR "src/3rdparty/tclap")
set(CF_CV_EXT_DIR "src/3rdparty/cv_ext")
set(CF_PIOTR_DIR "src/3rdparty/piotr")

include_directories(${OpenCV_DIR}/include)
include_directories(${CF_TCLAP_DIR}
                    ${CF_CV_EXT_DIR}
                    ${CF_PIOTR_DIR}
                    ${CF_PIOTR_DIR}/src
                    src/main)

include_directories(src/cf_libs/kcf
                    src/cf_libs/dsst
                    src/cf_libs/common)

set(CF_PIOTR_SOURCES
    ${CF_PIOTR_DIR}/gradientMex.hpp
    ${CF_PIOTR_DIR}/src/gradientMex.cpp
    ${CF_PIOTR_DIR}/src/sse.hpp
    ${CF_PIOTR_DIR}/src/wrappers.hpp)

set(CF_LIB_COMMON_SOURCES
    src/cf_libs/common/feature_channels.hpp
    src/cf_libs/common/mat_consts.hpp
    src/cf_libs/common/math_helper.hpp
    src/cf_libs/common/math_helper.cpp
    src/cf_libs/common/cf_tracker.hpp
    src/cf_libs/common/tracker_debug.hpp
    src/cf_libs/common/scale_estimator.hpp
    src/cf_libs/common/cv_ext.hpp
    ${CF_CV_EXT_DIR}/shift.cpp
    ${CF_CV_EXT_DIR}/shift.hpp
    ${CF_CV_EXT_DIR}/math_spectrums.cpp
    ${CF_CV_EXT_DIR}/math_spectrums.hpp
    ${CF_CV_EXT_DIR}/psr.hpp
    ${CF_PIOTR_SOURCES})

set(CF_MAIN_SOURCES
    src/main/image_acquisition.hpp
    src/main/image_acquisition.cpp
    ${CF_CV_EXT_DIR}/init_box_selector.hpp
    ${CF_CV_EXT_DIR}/init_box_selector.cpp
    ${CF_CV_EXT_DIR}/tracker_run.hpp
    ${CF_CV_EXT_DIR}/tracker_run.cpp
    ${CF_TCLAP_DIR}/tclap/CmdLine.h)

add_subdirectory(src)

add_subdirectory(src/main)