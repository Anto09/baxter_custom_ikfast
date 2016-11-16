cmake_minimum_required(VERSION 2.8.3)
project(baxter_ikfast_openrave)

## Find dependencies
find_package(catkin REQUIRED COMPONENTS roscpp std_msgs geometry_msgs tf message_generation)
find_package(OPENRAVE REQUIRED)
find_package(Eigen REQUIRED)
find_package(OpenMP REQUIRED)
find_package(LAPACK REQUIRED)

if(NOT OPENRAVE_FOUND)
  message(FATAL_ERROR "Could not find openrave")
else()
  message(STATUS "OPENRAVE_INCLUDE_DIRS : " ${OpenRAVE_INCLUDE_DIRS})
  message(STATUS "OPENRAVE_LIBRARIES : " ${OpenRAVE_LIBRARIES})
endif()

## Catkin setup
catkin_package(LIBRARIES ${PROJECT_NAME} CATKIN_DEPENDS roscpp std_msgs geometry_msgs tf message_runtime DEPENDS system_lib LAPACK)
## Set special library output path since we're making an openrave plugin
set(CMAKE_LIBRARY_OUTPUT_DIRECTORY ${CMAKE_CURRENT_SOURCE_DIR}/plugins)
## Include both default Catkin headers and OpenRAVE headers
include_directories(include ${catkin_INCLUDE_DIRS} ${OpenRAVE_INCLUDE_DIRS})
link_directories(${OpenRAVE_LIBRARY_DIRS})

## Declare a cpp library
add_library(${PROJECT_NAME} SHARED src/baxter_ikfastsolvers.cpp src/baxter_ikfastmodule.cpp src/baxter_ikfastsolver.cpp src/plugindefs.h src/ikfast.h src/baxter_leftarm_ikfast.cpp src/baxter_rightarm_ikfast.cpp)
set_target_properties(${PROJECT_NAME} PROPERTIES COMPILE_FLAGS "${PLUGIN_COMPILE_FLAGS}" LINK_FLAGS "${PLUGIN_LINK_FLAGS}")
target_link_libraries(${PROJECT_NAME} ${catkin_LIBRARIES} ${OpenRAVE_LIBRARIES} ${Boost_LIBRARIES} ${LAPACK_LIBRARIES})
add_dependencies(${PROJECT_NAME} ${catkin_EXPORTED_TARGETS} LAPACK)

## Mark library for installation
#install(TARGETS ${PROJECT_NAME}
#  ARCHIVE DESTINATION ${CATKIN_PACKAGE_LIB_DESTINATION}
#  LIBRARY DESTINATION ${CATKIN_PACKAGE_LIB_DESTINATION}
#  RUNTIME DESTINATION ${CATKIN_PACKAGE_BIN_DESTINATION}
#)
## Mark cpp header files for installation
#install(DIRECTORY include/${PROJECT_NAME}/
#  DESTINATION ${CATKIN_PACKAGE_INCLUDE_DESTINATION}
#  PATTERN ".svn" EXCLUDE
#)
