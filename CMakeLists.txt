cmake_minimum_required(VERSION 2.8.3)
project(dmp)

find_package(catkin REQUIRED 
    COMPONENTS message_generation std_msgs rospy roscpp eigen
)

# set(EXECUTABLE_OUTPUT_PATH ${PROJECT_SOURCE_DIR}/bin)
# set(LIBRARY_OUTPUT_PATH ${PROJECT_SOURCE_DIR}/lib)
include_directories(${catkin_INCLUDE_DIRS})
link_directories(${catkin_LIBRARY_DIRS})

# add message definitions
add_message_files(
  DIRECTORY msg 
  FILES DMPData.msg DMPPoint.msg DMPTraj.msg
)

# add service definitions
add_service_files(
  DIRECTORY srv 
  FILES GetDMPPlan.srv LearnDMPFromDemo.srv SetActiveDMP.srv
)

generate_messages(DEPENDENCIES std_msgs )

# find_package(Boost REQUIRED COMPONENTS thread)
include_directories(include ${Boost_INCLUDE_DIRS})

find_package(Eigen REQUIRED)
include_directories(${EIGEN_INCLUDE_DIRS})
add_definitions(${EIGEN_DEFINITIONS})

add_library(dmp src/dmp.cpp src/fourier_approx.cpp src/radial_approx.cpp)
add_executable(dmp_server nodes/dmp_server.cpp)
target_link_libraries(dmp_server dmp ${catkin_LIBRARIES})


catkin_package(
    DEPENDS std_msgs rospy roscpp eigen
    CATKIN_DEPENDS 
    INCLUDE_DIRS 
    LIBRARIES 
)

install(TARGETS dmp_server
  RUNTIME DESTINATION ${CATKIN_PACKAGE_BIN_DESTINATION})