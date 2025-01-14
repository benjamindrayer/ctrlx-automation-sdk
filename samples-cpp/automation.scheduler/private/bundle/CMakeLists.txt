
#
# Part of Manifestfile
#
set(BUNDLEX_NAME "com.boschrexroth.sdk.scheduler.bundle")
set(BUNDLEX_DISPLAY_NAME "com_boschrexroth_sdk_scheduler_bundle")
set(BUNDLEX_VERSION ${VERSION}) #Set bei CMakeLists.txt can be modified if more than one bundle is deployed
set(BUNDLEX_DESCRIPTION "ctrlX CORE Bundle")

#
# Set up source files and header
#
set(ACTIVATOR_NAME ${BUNDLEX_DISPLAY_NAME})

add_library(${ACTIVATOR_NAME} SHARED
  activator.cpp
  cmp1.cpp
)


#
# build dependencies before build activator and bundle
#
add_dependencies( ${ACTIVATOR_NAME} sdk_scheduler )

# don't set RPATH in shared library of activator because possibility wrong build paths in *.so
set_target_properties(${ACTIVATOR_NAME} PROPERTIES SKIP_BUILD_RPATH true)

#
#
#
target_include_directories( ${ACTIVATOR_NAME}
  PRIVATE ${LIBRARY_INCLUDES}
  PRIVATE .
  PRIVATE ./../impl
  PRIVATE ${CMAKE_SOURCE_DIR}/public/include
  PRIVATE ${USER_DEPENDENCY_DIR}/public/include/oss.celix
  PRIVATE ${USER_DEPENDENCY_DIR}/include/common.scheduler
  PRIVATE ${USER_DEPENDENCY_DIR}/include/comm.datalayer
  PRIVATE ${USER_DEPENDENCY_DIR}/include/common.log.trace
)

target_link_libraries(${ACTIVATOR_NAME} -Wl,--no-undefined -Wl,--whole-archive ${CELIX_DM_STATIC_CXX_LIB} -Wl,--no-whole-archive ${CELIX_LIBRARIES})
target_link_libraries(${ACTIVATOR_NAME}
  # *** Comment / uncomment SHARED libraries of components here ***
  sdk_scheduler
  #common_log_trace
  #common_log_trace_buffered_static

)

add_celix_bundle(${BUNDLEX_NAME}
  SYMBOLIC_NAME ${BUNDLEX_DISPLAY_NAME}
  VERSION ${BUNDLEX_VERSION}
  ACTIVATOR ${ACTIVATOR_NAME}
  DESCRIPTION ${BUNDLEX_DESCRIPTION}
  EXPORT_LIBRARIES
    sdk_scheduler
)

#
# Install target dep. generator type
#
# single-configuration generator (Unix Makefile)
message(STATUS "Bundle directrory: ${BUNDLEX_PUBLIC_INSTALL_DIR}")
install(FILES ${CMAKE_CURRENT_BINARY_DIR}/${BUNDLEX_NAME}.zip DESTINATION ${BUNDLEX_PUBLIC_INSTALL_DIR}/)
