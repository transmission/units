#parent must define VERSION_GTEST

cmake_minimum_required(VERSION 3.0.0 FATAL_ERROR)
PROJECT(gtest)
SET(TARGET_NAME gtest)
STRING(TOUPPER ${TARGET_NAME} TARGET_NAME_UPPER)

SET(VERSION_${TARGET_NAME_UPPER} "?.?.?" CACHE STRING "${TARGET_NAME} version string")
SET(${TARGET_NAME_UPPER}_DIR ${CMAKE_CURRENT_BINARY_DIR}/src CACHE STRING "${TARGET_NAME} source directory")

include(ExternalProject)
ExternalProject_Add(
	${TARGET_NAME}-ext
	URL ${CMAKE_CURRENT_SOURCE_DIR}/gtest-${VERSION_GTEST}.zip
	DOWNLOAD_DIR ${GTEST_DIR}
	SOURCE_DIR ${GTEST_DIR}
	PATCH_COMMAND ${PATCH} -p 0 -d ${GTEST_DIR} -i ${CMAKE_CURRENT_SOURCE_DIR}/patch.diff 
	INSTALL_COMMAND ""
	BUILD_IN_SOURCE 1
)

# ------------------------------------------------------------------------------------------------------------------------------
# EXPORT VARIABLES: Variables intended to help other projects find and use this library
# ------------------------------------------------------------------------------------------------------------------------------

# Library
add_library(${TARGET_NAME} STATIC IMPORTED GLOBAL)
IF(WIN32)
	SET_TARGET_PROPERTIES(${TARGET_NAME} PROPERTIES 
		IMPORTED_LOCATION_DEBUG ${GTEST_DIR}/Debug/${CMAKE_FIND_LIBRARY_PREFIXES}gtest${CMAKE_FIND_LIBRARY_SUFFIXES}
		IMPORTED_LOCATION_RELEASE ${GTEST_DIR}/Release/${CMAKE_FIND_LIBRARY_PREFIXES}gtest${CMAKE_FIND_LIBRARY_SUFFIXES}
		IMPORTED_LOCATION_MINSIZEREL ${GTEST_DIR}/MinSizeRel/${CMAKE_FIND_LIBRARY_PREFIXES}gtest${CMAKE_FIND_LIBRARY_SUFFIXES}
		IMPORTED_LOCATION_RELWITHDEBINFO ${GTEST_DIR}/RelWithDebInfo/${CMAKE_FIND_LIBRARY_PREFIXES}gtest${CMAKE_FIND_LIBRARY_SUFFIXES}
	)
ELSE(WIN32)
	SET_TARGET_PROPERTIES(${TARGET_NAME} PROPERTIES 
		IMPORTED_LOCATION ${GTEST_DIR}/${CMAKE_FIND_LIBRARY_PREFIXES}gtest${CMAKE_STATIC_LIBRARY_SUFFIX}
		INTERFACE_LINK_LIBRARIES -pthread
	)
ENDIF(WIN32)
add_dependencies(${TARGET_NAME} ${TARGET_NAME}-ext)

SET(${TARGET_NAME_UPPER}_INCLUDE_DIRS ${${TARGET_NAME_UPPER}_DIR}/include CACHE STRING "${TARGET_NAME} include directory")
SET(${TARGET_NAME_UPPER}_LIBRARIES ${TARGET_NAME} CACHE STRING "${TARGET_NAME} library location")

MARK_AS_ADVANCED(${TARGET_NAME_UPPER}_DIR ${TARGET_NAME_UPPER}_INCLUDE_DIRS ${TARGET_NAME_UPPER}_LIBRARIES)