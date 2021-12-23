# 3.21...3.22 because there is a new feature that I want to continue forward with at this version
cmake_minimum_required(VERSION 3.21...3.22)

include(CMakePrintHelpers)

project(OpenPlaya
	VERSION 0.1
	LANGUAGES C)

# The project version number.
set(VERSION_MAJOR   0   CACHE STRING "Project major version number.")
set(VERSION_MINOR   0   CACHE STRING "Project minor version number.")
set(VERSION_PATCH   1   CACHE STRING "Project patch version number.")
mark_as_advanced(VERSION_MAJOR VERSION_MINOR VERSION_PATCH)

if (CMAKE_PROJECT_NAME STREQUAL PROJECT_NAME)

	# Use stdlib
	set(CMAKE_C_EXTENSIONS OFF)

	# Support folders in IDEs
	set_property(GLOBAL PROPERTY USE_FOLDERS ON)

	# Test only for the main app
	include(CTest)

	# Docs only for the main app
	find_package(Doxygen OPTIONAL_COMPONENTS dot)
	if (Doxygen_FOUND)
		add_subdirectory(docs)
	else()
		message(STATUS "Doxygen not found, not building docs")
	endif()

	# Create output dir for run-time
	if(EXISTS "output")
		message(STATUS "Output dir already exists")
	else()
		message(STATUS "Created output dir")
		file(MAKE_DIRECTORY "output")
	endif()

endif()

# Fetch Content added as of 3.11, downloads during config step
include(FetchContent)

# The list of subdirectories where a CMakeLists.txt file exists to compile code prior to configuring the executable
add_subdirectory(core)

# Tests for the main app
if ((CMAKE_PROJECT_NAME STREQUAL PROJECT_NAME OR MODERN_CMAKE_BUILD_TESTING) AND BUILD_TESTING)
	add_subdirectory(tests)
endif()

# Configuration for the executable files and linking dependencies
add_executable(OpenPlaya core/src/main.c)

# Include and Link FFMpeg lib headers
target_include_directories(OpenPlaya PRIVATE ${AVCODEC_INCLUDE_DIR} ${AVFORMAT_INCLUDE_DIR})
target_link_libraries(OpenPlaya PRIVATE ${AVCODEC_LIBRARY} ${AVFORMAT_LIBRARY})
