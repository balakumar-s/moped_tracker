cmake_minimum_required(VERSION 2.8.3)
project(moped_tracker)

find_package(catkin REQUIRED COMPONENTS
  roscpp
  rospy
  cv_bridge
  image_transport
  pr_msgs
)
find_package(OpenCV REQUIRED)
MESSAGE(STATUS "OpenCV library is: ${OpenCV_VERSION}")

add_custom_target(BUILD_LIBMOPED DEPENDS libmoped.a )
add_custom_command(OUTPUT libmoped.a 
                   DEPENDS ${PROJECT_SOURCE_DIR}/libmoped/src/moped.cpp
                   DEPENDS ${PROJECT_SOURCE_DIR}/libmoped/src/config.hpp
                   DEPENDS ${PROJECT_SOURCE_DIR}/libmoped/src/util.hpp
                   DEPENDS ${PROJECT_SOURCE_DIR}/libmoped/src/STATUS_DISPLAY.hpp
                   DEPENDS ${PROJECT_SOURCE_DIR}/libmoped/src/GLOBAL_DISPLAY.hpp
                   COMMAND make
                   WORKING_DIRECTORY ${PROJECT_SOURCE_DIR}/libmoped
                   COMMENT Building libmoped.a)

link_directories(${PROJECT_SOURCE_DIR}/libmoped/lib)
include_directories(${PROJECT_SOURCE_DIR}/libmoped/include)

#
catkin_package(

)

include_directories(
  ${catkin_INCLUDE_DIRS}
)

add_executable(moped2 moped.cpp)
set_target_properties(moped2 PROPERTIES COMPILE_FLAGS "-O3 -Wall -march=native -mfpmath=sse -fopenmp")
set_target_properties(moped2 PROPERTIES LINK_FLAGS "-Wl,--start-group")
target_link_libraries(moped2 gomp GLEW glut IL ${PROJECT_SOURCE_DIR}/libmoped/lib/libmoped.a ${OpenCV_LIBS})


add_dependencies(moped2 BUILD_LIBMOPED)

add_custom_command(TARGET moped2 PRE_BUILD COMMAND ./find_opencv2.sh WORKING_DIRECTORY ..)

add_executable(moped2_test moped_test.cpp)
set_target_properties(moped2_test PROPERTIES COMPILE_FLAGS "-O3 -Wall -march=native -mfpmath=sse -fopenmp")
set_target_properties(moped2_test PROPERTIES LINK_FLAGS "-Wl,--start-group")
target_link_libraries(moped2_test gomp GLEW glut IL ${PROJECT_SOURCE_DIR}/libmoped/lib/libmoped.a ${OpenCV_LIBS})


add_dependencies(moped2_test BUILD_LIBMOPED)

add_custom_command(TARGET moped2 PRE_BUILD COMMAND ./find_opencv2.sh WORKING_DIRECTORY ..)


set(EXECUTABLE_OUTPUT_PATH ${PROJECT_SOURCE_DIR}/bin)

set(LIBRARY_OUTPUT_PATH ${PROJECT_SOURCE_DIR}/lib)
