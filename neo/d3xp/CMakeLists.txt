cmake_minimum_required (VERSION 3.10)
project(d3xp)

set(CMAKE_CXX_STANDARD 17)

# Visual studio specifics
if(MSVC)
	add_definitions(-DNOMINMAX)
endif(MSVC)

add_definitions(-DID_VULKAN)

# Clasify sources according to folder structure. Useful for having nice visual studio filters.
# This macro is derived from http://www.cmake.org/pipermail/cmake/2013-November/056336.html
macro(GroupSources curdir dirLabel)
	file(GLOB children RELATIVE ${PROJECT_SOURCE_DIR}/${curdir}
		${PROJECT_SOURCE_DIR}/${curdir}/*)
	foreach(child ${children})
		if(IS_DIRECTORY ${PROJECT_SOURCE_DIR}/${curdir}/${child})
			GroupSources(${curdir}/${child} ${dirLabel}/${child})
		else()
			string(REPLACE "/" "\\" groupname ${dirLabel})
			source_group(${groupname} FILES
				${PROJECT_SOURCE_DIR}/${curdir}/${child})
		endif()
	endforeach()
endmacro()

file(GLOB_RECURSE D3XP_SOURCE_FILES "*.cpp" "*.h")
GroupSources(./ src)
add_library(d3xp STATIC ${D3XP_SOURCE_FILES})
target_include_directories (d3xp PUBLIC ${CMAKE_CURRENT_SOURCE_DIR} )
target_precompile_headers (d3xp PUBLIC ../framework/precompiled.h)

find_package(Vulkan REQUIRED)
target_include_directories(d3xp PUBLIC ${Vulkan_INCLUDE_DIRS})