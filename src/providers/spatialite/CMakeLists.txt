########################################################
# Files

if(WITH_QSPATIALITE)
  SUBDIRS(${CMAKE_SOURCE_DIR}/external/qspatialite)
endif()

set(SPATIALITE_SRCS
  qgsspatialiteprovider.cpp
  qgsspatialitedataitems.cpp
  qgsspatialiteconnection.cpp
  qgsspatialiteconnpool.cpp
  qgsspatialitefeatureiterator.cpp
  qgsspatialitetablemodel.cpp
  qgsspatialiteproviderconnection.cpp
  qgsspatialitetransaction.cpp
)

if (WITH_GUI)
  set(SPATIALITE_SRCS ${SPATIALITE_SRCS}
    qgsspatialiteprovidergui.cpp
    qgsspatialitedataitemguiprovider.cpp
    qgsspatialitesourceselect.cpp
  )
endif()


########################################################
# Build

if (WITH_GUI)
  include_directories(SYSTEM
    ${QSCINTILLA_INCLUDE_DIR}
  )
endif()

include_directories(

  ${CMAKE_BINARY_DIR}/src/ui
)

add_library (spatialiteprovider MODULE ${SPATIALITE_SRCS})

# require c++17
target_compile_features(spatialiteprovider PRIVATE cxx_std_17)

target_link_libraries(spatialiteprovider
  qgis_core
  ${SPATIALITE_LIBRARY}
)

if (WITH_GUI)
  target_link_libraries (spatialiteprovider
    qgis_gui
  )
  add_dependencies(spatialiteprovider ui)
endif()

# clang-tidy
if(CLANG_TIDY_EXE)
  set_target_properties(
    spatialiteprovider PROPERTIES
    CXX_CLANG_TIDY "${DO_CLANG_TIDY}"
  )
endif()

########################################################
# Install

install(TARGETS spatialiteprovider
  RUNTIME DESTINATION ${QGIS_PLUGIN_DIR}
  LIBRARY DESTINATION ${QGIS_PLUGIN_DIR})
