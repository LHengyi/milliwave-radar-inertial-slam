cmake_minimum_required(VERSION 2.8.3)
project(sc_pgo)

set(CMAKE_BUILD_TYPE Debug)
# set(CMAKE_CXX_FLAGS "-std=c++20")
set(CMAKE_CXX_STANDARD 17)
set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -Wall -g -W -Wno-unused-parameter")
set(CMAKE_CXX_FLAGS_RELEASE "-Wall -g")

find_package(catkin REQUIRED COMPONENTS
  geometry_msgs
  nav_msgs
  sensor_msgs
  roscpp
  rospy
  rosbag
  std_msgs
  image_transport
  cv_bridge
  tf
)

find_package(Boost REQUIRED COMPONENTS
    program_options
)

find_package(Eigen3 REQUIRED PATHS /home/zotac/Library/eigen-3.3.9/install NO_DEFAULT_PATH)
find_package(PCL REQUIRED PATHS /home/zotac/Library/pcl-pcl-1.13.0/install NO_DEFAULT_PATH)
find_package(OpenCV REQUIRED)

find_package(OpenMP REQUIRED)
find_package(GTSAM REQUIRED QUIET PATHS /home/zotac/Library/gtsam-4.1.1/install NO_DEFAULT_PATH)

include_directories(
  include
	${catkin_INCLUDE_DIRS} 
	${PCL_INCLUDE_DIRS}
  ${OpenCV_INCLUDE_DIRS}
  ${GTSAM_INCLUDE_DIR}
  ${Boost_INCLUDE_DIRS}
)

catkin_package(
  CATKIN_DEPENDS geometry_msgs nav_msgs roscpp rospy std_msgs
  DEPENDS EIGEN3 PCL 
  INCLUDE_DIRS include
)

add_executable(alaserPGO 
  src/laserPosegraphOptimization.cpp
  include/scancontext/Scancontext.cpp
)
target_compile_options(alaserPGO 
  PRIVATE ${OpenMP_CXX_FLAGS}
)
target_link_libraries(alaserPGO 
  ${catkin_LIBRARIES} ${PCL_LIBRARIES}
  ${OpenMP_CXX_FLAGS}
  gtsam
)

add_executable(imuFactorTest
  src/ImuFactorTest.cpp
)
target_link_libraries(imuFactorTest 
  gtsam
  ${Boost_LIBRARIES}
  ${catkin_LIBRARIES} 
)

