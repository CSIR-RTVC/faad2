cmake_minimum_required(VERSION 3.11)
project(libfaad2 VERSION 1.0.0 LANGUAGES C CXX)

#Make sure that custom modules like FindRapidJSON are found
list(INSERT CMAKE_MODULE_PATH 0 ${CMAKE_SOURCE_DIR}/cmake)

SET( FAAD2_HDRS
    ./include/faad.h
    ./include/neaacdec.h
    ./libfaad/analysis.h
    ./libfaad/bits.h
    ./libfaad/cfft.h
    ./libfaad/cfft_tab.h
    ./libfaad/common.h
    ./libfaad/drc.h
    ./libfaad/drm_dec.h
    ./libfaad/error.h
    ./libfaad/filtbank.h
    ./libfaad/fixed.h
    ./libfaad/huffman.h
    ./libfaad/ic_predict.h
    ./libfaad/iq_table.h
    ./libfaad/is.h
    ./libfaad/kbd_win.h
    ./libfaad/lt_predict.h
    ./libfaad/mdct.h
    ./libfaad/mdct_tab.h
    ./libfaad/mp4.h
    ./libfaad/ms.h
    ./libfaad/output.h
    ./libfaad/pns.h
    ./libfaad/ps_dec.h
    ./libfaad/ps_tables.h
    ./libfaad/pulse.h
    ./libfaad/rvlc.h
    ./libfaad/sbr_dct.h
    ./libfaad/sbr_dec.h
    ./libfaad/sbr_e_nf.h
    ./libfaad/sbr_fbt.h
    ./libfaad/sbr_hfadj.h
    ./libfaad/sbr_hfgen.h
    ./libfaad/sbr_huff.h
    ./libfaad/sbr_noise.h
    ./libfaad/sbr_qmf.h
    ./libfaad/sbr_qmf_c.h
    ./libfaad/sbr_syntax.h
    ./libfaad/sbr_tf_grid.h
    ./libfaad/sine_win.h
    ./libfaad/specrec.h
    ./libfaad/ssr.h
    ./libfaad/ssr_fb.h
    ./libfaad/ssr_ipqf.h
    ./libfaad/ssr_win.h
    ./libfaad/structs.h
    ./libfaad/syntax.h
    ./libfaad/tns.h
    ./libfaad/codebook/hcb.h
    ./libfaad/codebook/hcb_1.h
    ./libfaad/codebook/hcb_10.h
    ./libfaad/codebook/hcb_11.h
    ./libfaad/codebook/hcb_2.h
    ./libfaad/codebook/hcb_3.h
    ./libfaad/codebook/hcb_4.h
    ./libfaad/codebook/hcb_5.h
    ./libfaad/codebook/hcb_6.h
    ./libfaad/codebook/hcb_7.h
    ./libfaad/codebook/hcb_8.h
    ./libfaad/codebook/hcb_9.h
    ./libfaad/codebook/hcb_sf.h
)

SET( FAAD2_SRCS
    ./libfaad/bits.c
    ./libfaad/cfft.c
    ./libfaad/common.c
    ./libfaad/decoder.c
    ./libfaad/drc.c
    ./libfaad/drm_dec.c
    ./libfaad/error.c
    ./libfaad/filtbank.c
    ./libfaad/hcr.c
    ./libfaad/huffman.c
    ./libfaad/ic_predict.c
    ./libfaad/is.c
    ./libfaad/lt_predict.c
    ./libfaad/mdct.c
    ./libfaad/mp4.c
    ./libfaad/ms.c
    ./libfaad/output.c
    ./libfaad/pns.c
    ./libfaad/ps_dec.c
    ./libfaad/ps_syntax.c
    ./libfaad/pulse.c
    ./libfaad/rvlc.c
    ./libfaad/sbr_dct.c
    ./libfaad/sbr_dec.c
    ./libfaad/sbr_e_nf.c
    ./libfaad/sbr_fbt.c
    ./libfaad/sbr_hfadj.c
    ./libfaad/sbr_hfgen.c
    ./libfaad/sbr_huff.c
    ./libfaad/sbr_qmf.c
    ./libfaad/sbr_syntax.c
    ./libfaad/sbr_tf_grid.c
    ./libfaad/specrec.c
    ./libfaad/ssr.c
    ./libfaad/ssr_fb.c
    ./libfaad/ssr_ipqf.c
    ./libfaad/syntax.c
    ./libfaad/tns.c
)

# interface library for now
add_library(libfaad2 STATIC
    ${FAAD2_SRCS} 
	${FAAD2_HDRS}
)


#Set target properties
target_include_directories(libfaad2
    PUBLIC
        $<INSTALL_INTERFACE:include>
        $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}/include/>
    PRIVATE
        ${CMAKE_CURRENT_SOURCE_DIR}/libfaad
)

# get rid of some compiler warnings
TARGET_COMPILE_DEFINITIONS(libfaad2 
    PRIVATE 
        "-D_CRT_SECURE_NO_WARNINGS" "-D_LIB" "-DHAVE_LRINTF"
		)

TARGET_COMPILE_FEATURES(libfaad2 PRIVATE cxx_auto_type)
TARGET_COMPILE_OPTIONS(libfaad2 PRIVATE $<$<CXX_COMPILER_ID:GNU>:-Wall>)

target_link_libraries(libfaad2
    PUBLIC
    PRIVATE
)

##############################################
# Installation instructions

include(GNUInstallDirs)
set(INSTALL_CONFIGDIR ${CMAKE_INSTALL_LIBDIR}/cmake/libfaad2)

install(TARGETS libfaad2
    EXPORT libfaad2-targets
    LIBRARY DESTINATION ${CMAKE_INSTALL_LIBDIR}
    ARCHIVE DESTINATION ${CMAKE_INSTALL_LIBDIR}
)

install(DIRECTORY include/ DESTINATION ${CMAKE_INSTALL_INCLUDEDIR})

#Export the targets to a script
install(EXPORT libfaad2-targets
  FILE
    libfaad2Targets.cmake
  NAMESPACE
    libfaad2::
  DESTINATION
    ${INSTALL_CONFIGDIR}
)

#Create a ConfigVersion.cmake file
include(CMakePackageConfigHelpers)
write_basic_package_version_file(
    ${CMAKE_CURRENT_BINARY_DIR}/libfaad2ConfigVersion.cmake
    VERSION ${PROJECT_VERSION}
    COMPATIBILITY AnyNewerVersion
)

configure_package_config_file(${CMAKE_CURRENT_LIST_DIR}/cmake/libfaad2Config.cmake.in
    ${CMAKE_CURRENT_BINARY_DIR}/libfaad2Config.cmake
    INSTALL_DESTINATION ${INSTALL_CONFIGDIR}
)

#Install the config, configversion and custom find modules
install(FILES
    ${CMAKE_CURRENT_BINARY_DIR}/libfaad2Config.cmake
    ${CMAKE_CURRENT_BINARY_DIR}/libfaad2ConfigVersion.cmake
    DESTINATION ${INSTALL_CONFIGDIR}
)

##############################################
export(EXPORT libfaad2-targets FILE ${CMAKE_CURRENT_BINARY_DIR}/libfaad2Targets.cmake NAMESPACE libfaad2::)

#Register package in user's package registry
export(PACKAGE libfaad2)
