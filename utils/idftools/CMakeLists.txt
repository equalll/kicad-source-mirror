include_directories(
    "${CMAKE_SOURCE_DIR}/include"
    "${CMAKE_SOURCE_DIR}/dxflib_qcad"
    "${CMAKE_SOURCE_DIR}/utils/idftools"
    ${OPENGL_INCLUDE_DIR}
    ${Boost_INCLUDE_DIR}
)

link_directories(
    "${CMAKE_BINARY_DIR}/lib_dxf"
)

set( IDF3_FILES
    idf_helpers.cpp
    idf_common.cpp
    idf_outlines.cpp
    idf_parser.cpp
    vrml_layer.cpp
)

if( MINGW )
    list( APPEND IDF3_FILES ${CMAKE_SOURCE_DIR}/common/streamwrapper.cpp )
endif( MINGW )

add_library( idf3 STATIC ${IDF3_FILES} )

add_executable( idfcyl idf_cylinder.cpp )
add_executable( idfrect idf_rect.cpp )
add_executable( dxf2idf dxf2idfmain.cpp dxf2idf.cpp )
add_executable( idf2vrml idf2vrml.cpp )

target_link_libraries( dxf2idf lib_dxf idf3 ${wxWidgets_LIBRARIES} )

target_link_libraries( idf2vrml idf3 common ${OPENGL_LIBRARIES} ${wxWidgets_LIBRARIES} )

if( APPLE )
    # puts binaries into the *.app bundle while linking
    set_target_properties( idfcyl idfrect dxf2idf idf2vrml PROPERTIES
        RUNTIME_OUTPUT_DIRECTORY ${OSX_BUNDLE_BUILD_BIN_DIR}
        )
else()
    install( TARGETS idfcyl idfrect idf2vrml
        dxf2idf
        DESTINATION ${KICAD_BIN}
        COMPONENT binary )
endif()
