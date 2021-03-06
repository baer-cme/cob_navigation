cmake_minimum_required(VERSION 2.8.3)
project(cob_map_accessibility_analysis)

set(catkin_RUN_PACKAGES
  cob_3d_mapping_msgs
  cv_bridge
  geometry_msgs
  image_transport
  message_filters
  nav_msgs
  pcl_ros
  roscpp
  sensor_msgs
  tf
)

set(catkin_BUILD_PACKAGES
	${catkin_RUN_PACKAGES}
	message_generation
)

find_package(catkin REQUIRED COMPONENTS
  ${catkin_BUILD_PACKAGES}
)

find_package(Boost REQUIRED)
find_package(OpenCV REQUIRED)

catkin_python_setup()

add_service_files(
  FILES
    CheckPerimeterAccessibility.srv
    CheckPointAccessibility.srv
)

generate_messages(
  DEPENDENCIES
    geometry_msgs
)

catkin_package(
INCLUDE_DIRS
	ros/include
LIBRARIES
	map_accessibility_analysis
CATKIN_DEPENDS
	${catkin_RUN_PACKAGES}
	message_runtime
DEPENDS
	Boost
	OpenCV
)

include_directories(
  ros/include
  ${catkin_INCLUDE_DIRS}
  ${Boost_INCLUDE_DIRS}
  ${OpenCV_INCLUDE_DIRS}
)

# map_accessibility_analysis lib
add_library(map_accessibility_analysis
  ros/src/map_accessibility_analysis.cpp
)
target_link_libraries(map_accessibility_analysis
  ${catkin_LIBRARIES}
  ${Boost_LIBRARIES}
  ${OpenCV_LIBRARIES}
)
add_dependencies(map_accessibility_analysis ${catkin_EXPORTED_TARGETS})

# map_accessibility_analysis_server
add_executable(map_accessibility_analysis_server
  ros/src/map_accessibility_analysis_server.cpp
)
target_link_libraries(map_accessibility_analysis_server
  ${catkin_LIBRARIES}
  ${Boost_LIBRARIES}
  ${OpenCV_LIBRARIES}
  map_accessibility_analysis
)
add_dependencies(map_accessibility_analysis_server ${${PROJECT_NAME}_EXPORTED_TARGETS} ${catkin_EXPORTED_TARGETS})


# map_accessibility_analysis_client
add_executable(map_accessibility_analysis_client ros/src/map_accessibility_analysis_client.cpp)
target_link_libraries(map_accessibility_analysis_client
  ${catkin_LIBRARIES}
  ${Boost_LIBRARIES}
  ${OpenCV_LIBRARIES}
)
add_dependencies(map_accessibility_analysis_client ${${PROJECT_NAME}_EXPORTED_TARGETS} ${catkin_EXPORTED_TARGETS})

#############
## Install ##
#############
install(TARGETS map_accessibility_analysis_server map_accessibility_analysis_client
  ARCHIVE DESTINATION ${CATKIN_PACKAGE_LIB_DESTINATION}
  LIBRARY DESTINATION ${CATKIN_PACKAGE_LIB_DESTINATION}
  RUNTIME DESTINATION ${CATKIN_PACKAGE_BIN_DESTINATION}
)

install(DIRECTORY ros/include/${PROJECT_NAME}/
  DESTINATION ${CATKIN_PACKAGE_INCLUDE_DESTINATION}
)

install(DIRECTORY ros/launch
  DESTINATION ${CATKIN_PACKAGE_SHARE_DESTINATION}/ros
)
