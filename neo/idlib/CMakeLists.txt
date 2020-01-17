cmake_minimum_required (VERSION 3.10)
project(idlib)

set(CMAKE_CXX_STANDARD 17)

# Visual studio specifics
if(MSVC)
	add_definitions(-DNOMINMAX)
endif(MSVC)

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

file(GLOB_RECURSE IDLIB_SOURCE_FILES "*.cpp" "*.h")
GroupSources(./ src)
add_library(idlib STATIC ${IDLIB_SOURCE_FILES})
target_include_directories (idlib PUBLIC ${CMAKE_CURRENT_SOURCE_DIR} )