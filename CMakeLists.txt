cmake_minimum_required(VERSION 3.18)

include(${CMAKE_CURRENT_SOURCE_DIR}/cmake/versioning.cmake)

project(
	F4SE_Plugin_Template
	VERSION ${F4SEVRTEMPLATE_VERSION_MAJOR}.${F4SEVRTEMPLATE_VERSION_MINOR}.${F4SEVRTEMPLATE_VERSION_PATCH}
	LANGUAGES CXX
)

# ---- Include guards ----

if(PROJECT_SOURCE_DIR STREQUAL PROJECT_BINARY_DIR)
	message(
		FATAL_ERROR
			"In-source builds not allowed. Please make a new directory (called a build directory) and run CMake from there."
)
endif()

# ---- Dependencies ---- 
include_directories(../common)
include_directories(./include)

if (NOT TARGET f4se)
	add_subdirectory(${CMAKE_CURRENT_SOURCE_DIR}/../f4sevr/f4se f4se)	# bundled
endif()

if (NOT TARGET f4se_common)
	add_subdirectory(${CMAKE_CURRENT_SOURCE_DIR}/../f4sevr/f4se_common f4se_common)	# bundled
endif()

if (NOT TARGET xbyak)
	add_subdirectory(${CMAKE_CURRENT_SOURCE_DIR}/../f4sevr/xbyak xbyak)	# bundled
endif()

# ---- Plugin source files include ----

include(cmake/headerlist.cmake)
include(cmake/sourcelist.cmake)

add_library(${PROJECT_NAME} SHARED "${sources}")

set_target_properties(
	${PROJECT_NAME}
	PROPERTIES
		OUTPUT_NAME "${PROJECT_NAME}_${F4_VERSION_MAJOR}_${F4_VERSION_MINOR}_${F4_VERSION_PATCH}"
)

target_compile_definitions(
	${PROJECT_NAME}
	PRIVATE
		RUNTIME
		RUNTIME_VERSION=${F4_VERSION_PACKED}
)

target_compile_features(
	${PROJECT_NAME}
	PUBLIC
		cxx_std_11
)

target_include_directories(${PROJECT_NAME} PUBLIC include)

target_link_libraries(
	${PROJECT_NAME}
	PUBLIC
		f4se::f4se_common
		Xbyak::xbyak
)
