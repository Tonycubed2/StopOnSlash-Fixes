cmake_minimum_required(VERSION 3.21)

# StopOnSlash (dTryHitStop) - upstream source from github.com/doodlum/StopOnSlash
# (commit 295013d, Aug 22 2026, 1.7.99 support). Only the build setup is changed:
# it builds against the local CommonLibSSE-NG 16.2.0 (upstream pinned 6.6.0; 6.7.0 called Actor
# virtuals past slot 0x88 one slot late, e.g. IsDead ran CreateAnimNoteReceiver).
project(dTryHitStop VERSION 2.2.1 LANGUAGES CXX)

if("${PROJECT_SOURCE_DIR}" STREQUAL "${PROJECT_BINARY_DIR}")
	message(FATAL_ERROR "in-source builds are not allowed")
endif()

if(DEFINED ENV{CommonLibSSEPath_NG} AND IS_DIRECTORY "$ENV{CommonLibSSEPath_NG}")
	set(COMMONLIB_DIR "$ENV{CommonLibSSEPath_NG}")
else()
	set(COMMONLIB_DIR "C:/dllmaker/CommonLibSSE-NG")
endif()
message(STATUS "Using CommonLibSSE-NG from: ${COMMONLIB_DIR}")

set(ENABLE_SKYRIM_SE ON CACHE BOOL "" FORCE)
set(ENABLE_SKYRIM_AE ON CACHE BOOL "" FORCE)
set(ENABLE_SKYRIM_VR OFF CACHE BOOL "" FORCE)
set(BUILD_TESTS OFF CACHE BOOL "" FORCE)
set(SKSE_SUPPORT_PATCH_SAFETY OFF CACHE BOOL "" FORCE)

add_subdirectory("${COMMONLIB_DIR}" CommonLibSSE EXCLUDE_FROM_ALL)

add_library(${PROJECT_NAME} SHARED)

list(APPEND CMAKE_MODULE_PATH "${PROJECT_SOURCE_DIR}/cmake")
include(AddCXXFiles)
add_cxx_files(${PROJECT_NAME})

configure_file(cmake/Plugin.h.in "${CMAKE_CURRENT_BINARY_DIR}/cmake/Plugin.h" @ONLY)
configure_file(cmake/version.rc.in "${CMAKE_CURRENT_BINARY_DIR}/cmake/version.rc" @ONLY)
target_sources(${PROJECT_NAME}
	PRIVATE
		"${CMAKE_CURRENT_BINARY_DIR}/cmake/Plugin.h"
		"${CMAKE_CURRENT_BINARY_DIR}/cmake/version.rc"
)

target_compile_features(${PROJECT_NAME} PRIVATE cxx_std_23)
target_compile_definitions(${PROJECT_NAME} PRIVATE SKYRIM _UNICODE)

target_compile_options(${PROJECT_NAME}
	PRIVATE
		/MP
		/permissive-
		/Zc:preprocessor
		/EHsc
		/utf-8
		/wd4200
		$<$<CONFIG:Release>:/Zi>
)

target_link_options(${PROJECT_NAME}
	PRIVATE
		$<$<CONFIG:Release>:/DEBUG:FULL /OPT:REF /OPT:ICF /INCREMENTAL:NO>
)

target_precompile_headers(${PROJECT_NAME} PRIVATE include/PCH.h)

find_path(SIMPLEINI_INCLUDE_DIRS "ConvertUTF.c")

target_include_directories(${PROJECT_NAME}
	PRIVATE
		"${CMAKE_CURRENT_SOURCE_DIR}/include"
		"${CMAKE_CURRENT_SOURCE_DIR}/src"
		"${CMAKE_CURRENT_BINARY_DIR}/cmake"
		${SIMPLEINI_INCLUDE_DIRS}
)

target_link_libraries(${PROJECT_NAME} PRIVATE CommonLibSSE::CommonLibSSE)

# No automatic copy: the finished DLL stays in build\Release\
