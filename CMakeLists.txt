project(X3GWriter)
cmake_minimum_required(VERSION 3.2.3) #Only version it's tested with.

#Project metadata.
set(X3GWRITER_PLUGIN_NAME X3GWriter CACHE STRING "The name of the plug-in in distribution.")
set(X3GWRITER_VERSION_MAJOR 2 CACHE STRING "The major version number.")
set(X3GWRITER_VERSION_MINOR 6 CACHE STRING "The minor version number.")
set(X3GWRITER_VERSION_PATCH 0 CACHE STRING "The patch version number.")

#Dependencies.
include(ExternalProject)
ExternalProject_Add(GPX
	GIT_REPOSITORY https://github.com/Ghostkeeper/GPX.git
	GIT_TAG feature_cmake
	CMAKE_ARGS -DCMAKE_INSTALL_PREFIX=${CMAKE_INSTALL_PREFIX} -DCMAKE_BUILD_TYPE=${CMAKE_BUILD_TYPE} -DCMAKE_PREFIX_PATH=${CMAKE_PREFIX_PATH} -G ${CMAKE_GENERATOR}
)
if(WIN32)
	find_program(GPX_EXECUTABLE gpx.exe PATHS "${CMAKE_CURRENT_BINARY_DIR}" "${CMAKE_INSTALL_PREFIX}/GPX/bin" "${CMAKE_INSTALL_PREFIX}/bin")
else()
	find_program(GPX_EXECUTABLE gpx PATHS "${CMAKE_CURRENT_BINARY_DIR}" "${CMAKE_INSTALL_PREFIX}/bin")
endif()
mark_as_advanced(GPX_EXECUTABLE)

#Installation.
configure_file(${CMAKE_CURRENT_SOURCE_DIR}/plugin.json.in plugin.json)
set(installed_files
	${CMAKE_CURRENT_SOURCE_DIR}/__init__.py
	${CMAKE_CURRENT_SOURCE_DIR}/cfg.ini
	${CMAKE_CURRENT_SOURCE_DIR}/param.ini
	${CMAKE_CURRENT_SOURCE_DIR}/X3GWriter.py
	${CMAKE_CURRENT_BINARY_DIR}/plugin.json
	${GPX_EXECUTABLE}
)
install(FILES ${installed_files} DESTINATION X3GWriter)

#Packaging, create a .curaplugin file.
include(InstallRequiredSystemLibraries)
set(CPACK_PACKAGE_VERSION_MAJOR ${X3GWRITER_VERSION_MAJOR})
set(CPACK_PACKAGE_VERSION_MINOR ${X3GWRITER_VERSION_MINOR})
set(CPACK_PACKAGE_VERSION_PATCH ${X3GWRITER_VERSION_PATCH})
set(CPACK_PACKAGE_NAME ${X3GWRITER_PLUGIN_NAME})
set(CPACK_PACKAGE_DESCRIPTION_FILE ${CMAKE_CURRENT_SOURCE_DIR}/README.md)
set(CPACK_GENERATOR ZIP)
include(CPack)