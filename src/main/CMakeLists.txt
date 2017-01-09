message("LIBKCF=${LIBKCF}")

if(ANDROID_NDK OR ARM_LINUX_SYSROOT)
	add_executable(DSSTcpp
    cmd_dsst.cpp
#    src/cf_libs/dsst/dsst_debug.hpp
#    src/cf_libs/dsst/dsst_tracker.hpp
#    ${CF_MAIN_SOURCES}
#    ${CF_LIB_COMMON_SOURCES}
    )

	target_link_libraries(DSSTcpp
	${LIBKCF}
#	"/home/thomas/build/bcv-ndk.gl/out/cv/kcf/libKCF.a"
	${OpenCV_LIBRARIES}
	)

	add_executable(KCFcpp
    cmd_kcf.cpp
#    src/cf_libs/kcf/kcf_debug.hpp
#    src/cf_libs/kcf/kcf_tracker.hpp
#    ${CF_MAIN_SOURCES}
#    ${CF_LIB_COMMON_SOURCES}
    )

    target_link_libraries(KCFcpp
	${LIBKCF}
#	"/home/thomas/build/bcv-ndk.gl/out/cv/kcf/libKCF.a"
	${OpenCV_LIBRARIES})

else()
	add_executable(DSSTcpp
		src/main/main_dsst.cpp
		src/cf_libs/dsst/dsst_debug.hpp
		src/cf_libs/dsst/dsst_tracker.hpp
		${CF_MAIN_SOURCES}
		${CF_LIB_COMMON_SOURCES})
	qt5_use_modules(DSSTcpp Widgets  OpenGL Test Concurrent)
	target_link_libraries(DSSTcpp ${OpenCV_LIBS})

	add_executable(KCFcpp
		src/main/main_kcf.cpp
		src/cf_libs/kcf/kcf_debug.hpp
		src/cf_libs/kcf/kcf_tracker.hpp
		${CF_MAIN_SOURCES}
		${CF_LIB_COMMON_SOURCES})
	qt5_use_modules(KCFcpp Widgets  OpenGL Test Concurrent)
	target_link_libraries(KCFcpp ${OpenCV_LIBS})
endif()
