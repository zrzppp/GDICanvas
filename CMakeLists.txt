cmake_minimum_required(VERSION 3.0)

set(LIB_NAME GDICanvas)
project(${LIB_NAME} CXX)

set(CMAKE_CXX_COMPILER g++)
set(CMAKE_MAKE_PROGRAM mingw32-make)

# File locations
set(SRC_DIR src)
set(INC_DIR include)
set(BUILD_DIR ../../build)

set(CMAKE_ARCHIVE_OUTPUT_DIRECTORY ${BUILD_DIR}/lib)
set(CMAKE_LIBRARY_OUTPUT_DIRECTORY ${BUILD_DIR}/lib)

# Flags for MinGW
IF(CMAKE_COMPILER_IS_GNUCXX)
    set(CMAKE_CXX_FLAGS "-lstdc++ -Wall -pedantic -Wextra -std=c++11 -mwindows -lwinmm")
ENDIF(CMAKE_COMPILER_IS_GNUCXX)

set_source_files_properties(logo.ico logo.rc PROPERTIES LANGUAGE RC)

if(MINGW)
    set(CMAKE_RC_COMPILER_INIT windres)
    enable_language(RC)
    set(CMAKE_RC_COMPILE_OBJECT
        "<CMAKE_RC_COMPILER> <FLAGS> -O coff <DEFINES> -i <SOURCE> -o <OBJECT>")
endif(MINGW)

set(EXECUTABLE_OUTPUT_PATH ${CMAKE_CURRENT_SOURCE_DIR})

# Source Files
set(CXX_FILES
    ${SRC_DIR}/Canvas.cxx
    ${SRC_DIR}/Colors.cxx
    ${SRC_DIR}/Shapes.cxx
    ${SRC_DIR}/Vec2D.cxx
    ${SRC_DIR}/logo.rc
    )

# Include files. To be copied to the build folder
set(INCLUDE_FILES
    src/Canvas.h
    src/Colors.h
    src/Shapes.h
    src/Vec2D.h
    src/VirtualKeys.h
    src/logo.h
    )

add_subdirectory(examples)

add_library(${LIB_NAME} STATIC ${CXX_FILES} ${INCLUDE_FILES})
set_target_properties(${LIB_NAME} PROPERTIES LINKER_LANGUAGE CXX)

# Copy the include files to the build folder
file(COPY ${INCLUDE_FILES} DESTINATION ${BUILD_DIR}/include)

add_executable(demo WIN32 demo.cxx src/logo.rc)
target_link_libraries(demo ${LIB_NAME})
target_include_directories(demo PUBLIC build/include)
