cmake_minimum_required(VERSION 3.21)
set_property(GLOBAL PROPERTY USE_FOLDERS ON)

##
# Function that generates source groups from directories
##
function(GenerateSourceGoups Sources)
	foreach(Source ${Sources})
		get_filename_component(SourcePath "${Source}" PATH)
		if (WIN32)
			string(REPLACE "/" "\\" SourcePath "${SourcePath}")
		endif()
		source_group("${SourcePath}" FILES "${Source}")
	endforeach()
endfunction()

##
# Start
##
project(s4_assets_manager VERSION "1.0.0")
enable_testing()

set(CMAKE_CXX_STANDARD 20)
set(CMAKE_CXX_STANDARD_REQUIRED ON)

set(S4_ASSETS_MANAGER_PROJECT_ROOT_DIR ${CMAKE_SOURCE_DIR})
set(S4_ASSETS_MANAGER_PROJECT_DIST_DIR "${S4_ASSETS_MANAGER_PROJECT_ROOT_DIR}/dist")
set(S4_ASSETS_MANAGER_PROJECT_COMMON_BUILD_DIR "${S4_ASSETS_MANAGER_PROJECT_ROOT_DIR}/common_build")

if(MSVC)
  add_compile_options(/W4 /WX)
else()
  set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -std=c++2a")
  add_compile_options(-Wall -Wextra -Wpedantic -Werror -Wno-unused-variable)
endif()

# Output dirs
if (WIN32)
	set(S4_ASSETS_MANAGER_PROJECT_FULL_DIST_DIR "${S4_ASSETS_MANAGER_PROJECT_DIST_DIR}") # Config name is added autmatically on windows
else()
	set(S4_ASSETS_MANAGER_PROJECT_FULL_DIST_DIR "${S4_ASSETS_MANAGER_PROJECT_DIST_DIR}/${BUILD_CONFIG_NAME}")
endif()
	
set(CMAKE_ARCHIVE_OUTPUT_DIRECTORY ${S4_ASSETS_MANAGER_PROJECT_FULL_DIST_DIR})
set(CMAKE_LIBRARY_OUTPUT_DIRECTORY ${S4_ASSETS_MANAGER_PROJECT_FULL_DIST_DIR})
set(CMAKE_RUNTIME_OUTPUT_DIRECTORY ${S4_ASSETS_MANAGER_PROJECT_FULL_DIST_DIR})

# Fetch dependencies
include(FetchContent)
FetchContent_Declare(
  Catch2
  GIT_REPOSITORY "https://github.com/catchorg/Catch2.git"
  GIT_TAG "v3.0.0-preview3"
)
include(FetchContent)
FetchContent_Declare(
  stduuid
  GIT_REPOSITORY "https://github.com/mariusbancila/stduuid.git"
  GIT_TAG "4959d46eb494f8eddd4a37a1c8c6434b41d56ca8"
)
include(FetchContent)
FetchContent_Declare(
  s4_rtti
  GIT_REPOSITORY "https://github.com/PiotrMoscicki/s4_rtti.git"
  GIT_TAG "dev"
)

FetchContent_MakeAvailable(Catch2 stduuid s4_rtti)
list(APPEND CMAKE_MODULE_PATH ${catch2_SOURCE_DIR}/extras)

# Target names
set(S4_ASSETS_MANAGER_TARGET s4_assets_manager)
set(S4_ASSETS_MANAGER_EXAMPLE_TARGET s4_assets_manager_example)
set(S4_ASSETS_MANAGER_TEST_TARGET s4_assets_manager_test)

# Subdirectories
add_subdirectory(src)
add_subdirectory(example)
add_subdirectory(test)