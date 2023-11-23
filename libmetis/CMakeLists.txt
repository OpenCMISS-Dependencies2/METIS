
# Find sources.
file(GLOB metis_sources *.c)

# Build libmetis.
add_library(METIS ${METIS_LIBRARY_TYPE} ${metis_sources})

target_link_libraries(METIS PUBLIC GKlib::GKlib)

target_include_directories(METIS
  PRIVATE
  $<BUILD_INTERFACE:${PROJECT_SOURCE_DIR}/include>
  $<BUILD_INTERFACE:${PROJECT_SOURCE_DIR}/libmetis/include>
  INTERFACE
  $<INSTALL_INTERFACE:${CMAKE_INSTALL_INCLUDEDIR}>
)

include(CMakePackageConfigHelpers)

configure_package_config_file("${CMAKE_CURRENT_LIST_DIR}/${PROJECT_NAME}Config.cmake.in"
  "${CMAKE_CURRENT_BINARY_DIR}/${PROJECT_NAME}Config.cmake"
  INSTALL_DESTINATION "cmake/"
)

write_basic_package_version_file("${CMAKE_CURRENT_BINARY_DIR}/${PROJECT_NAME}ConfigVersion.cmake"
  VERSION ${METIS_VERSION}
  COMPATIBILITY AnyNewerVersion
)

if(METIS_INSTALL)
  
  install(TARGETS METIS
    EXPORT "${PROJECT_NAME}Targets"
    LIBRARY DESTINATION "${CMAKE_INSTALL_LIBDIR}"
    ARCHIVE DESTINATION "${CMAKE_INSTALL_LIBDIR}"
    INCLUDES DESTINATION "${CMAKE_INSTALL_INCLUDEDIR}"
  )
  
  install(FILES ${METIS_PATH}/include/metis.h
    DESTINATION "${CMAKE_INSTALL_INCLUDEDIR}")
  
  install(FILES "${CMAKE_CURRENT_BINARY_DIR}/${PROJECT_NAME}Config.cmake" "${CMAKE_CURRENT_BINARY_DIR}/${PROJECT_NAME}ConfigVersion.cmake"
    DESTINATION "cmake/${PROJECT_NAME}"
  )
  
  install(EXPORT "${PROJECT_NAME}Targets"
    NAMESPACE "${PROJECT_NAME}::"
    DESTINATION "cmake/"
  )

endif()
