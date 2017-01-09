cmake_minimum_required(VERSION 2.8)

# add 3rdparty source
set(CF_TCLAP_DIR "3rdparty/tclap")
set(CF_CV_EXT_DIR "3rdparty/cv_ext")
set(CF_PIOTR_DIR "3rdparty/piotr")

#include_directories(${OpenCV_DIR}/include)
include_directories(${CF_TCLAP_DIR}
                    ${CF_CV_EXT_DIR}
                    ${CF_PIOTR_DIR}
                    ${CF_PIOTR_DIR}/src
                    main)

include_directories(cf_libs/kcf
                    cf_libs/dsst
                    cf_libs/common)

set(CF_PIOTR_SOURCES
#    ${CF_PIOTR_DIR}/gradientMex.hpp
    ${CF_PIOTR_DIR}/src/gradientMex.cpp
#    ${CF_PIOTR_DIR}/src/sse.hpp
#    ${CF_PIOTR_DIR}/src/wrappers.hpp
    )

set(CF_LIB_COMMON_SOURCES
#    cf_libs/common/feature_channels.hpp
#    cf_libs/common/mat_consts.hpp
#    cf_libs/common/math_helper.hpp
    cf_libs/common/math_helper.cpp
#    cf_libs/common/cf_tracker.hpp
#    cf_libs/common/tracker_debug.hpp
#    cf_libs/common/scale_estimator.hpp
#    cf_libs/common/cv_ext.hpp
    ${CF_CV_EXT_DIR}/shift.cpp
#    ${CF_CV_EXT_DIR}/shift.hpp
    ${CF_CV_EXT_DIR}/math_spectrums.cpp
#    ${CF_CV_EXT_DIR}/math_spectrums.hpp
#    ${CF_CV_EXT_DIR}/psr.hpp
    ${CF_PIOTR_SOURCES})

set(CF_MAIN_SOURCES
#    main/image_acquisition.hpp
    main/image_acquisition.cpp
#    ${CF_CV_EXT_DIR}/init_box_selector.hpp
    ${CF_CV_EXT_DIR}/init_box_selector.cpp
#    ${CF_CV_EXT_DIR}/tracker_run.hpp
    ${CF_CV_EXT_DIR}/tracker_run.cpp
#    ${CF_TCLAP_DIR}/tclap/CmdLine.h
    )

add_library(LIBKCF STATIC
#	-Wl,--whole-archive
	${CF_MAIN_SOURCES}
	${CF_LIB_COMMON_SOURCES}
#	-Wl,--no-whole-archive
	)
#target_link_libraries(LIBKCF ${OpenCV_LIBS})
#set_target_properties(LIBKCF PROPERTIES VERSION 1.0.0 SOVERSION 1)
message("LIBKCF=${LIBKCF}")