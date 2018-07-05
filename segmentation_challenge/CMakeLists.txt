cmake_minimum_required(VERSION 2.8.9)
project(segmentation)

#Bring the headers, such as mask_generator.h etc into the project
include_directories(include)
include(CheckCXXCompilerFlag)
CHECK_CXX_COMPILER_FLAG("-std=c++14" COMPILER_SUPPORTS_CXX14)
CHECK_CXX_COMPILER_FLAG("-std=c++0x" COMPILER_SUPPORTS_CXX0X)
if(COMPILER_SUPPORTS_CXX14)
    set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -std=c++14")
elseif(COMPILER_SUPPORTS_CXX0X)
    set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -std=c++0x")
else()
        message(STATUS "The compiler ${CMAKE_CXX_COMPILER} has no C++14 support. Please use a different C++ compiler.")
endif()

#However, the file(GLOB...) allows for wildcard additions:
file(GLOB SOURCES "src/*.cpp")

set(Boost_USE_STATIC_LIBS ON) 
set(Boost_USE_MULTITHREADED ON)  
set(Boost_USE_STATIC_RUNTIME OFF) 
find_package( Boost REQUIRED COMPONENTS program_options regex system)
include_directories( ${Boost_INCLUDE_DIRS} )
link_directories( ${Boost_LIBRARY_DIRS})

find_package(PNG REQUIRED)
include_directories(${PNG_INCLUDE_DIR})

find_package(blaze REQUIRED)
include_directories(${BLAZE_INCLUDE_DIR})

find_package(BLAS)
find_package(LAPACK)
if(LAPACK_FOUND AND BLAS_FOUND)
   set(lapackblas_libraries "${BLAS_LIBRARIES} ${LAPACK_LIBRARIES}")
endif()


add_executable(seg ${SOURCES})

target_link_libraries( seg ${PNG_LIBRARY} ${BLAZE_INCLUDE_DIR} ${Boost_PROGRAM_OPTIONS_LIBRARY} ${Boost_REGEX_LIBRARY} ${Boost_SYSTEM_LIBRARY} ${BLAS_LIBRARIES} ${LAPACK_LIBRARIES})
