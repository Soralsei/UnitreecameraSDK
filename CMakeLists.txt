cmake_minimum_required(VERSION 2.8.3)
project(UnitreeCameraSDK)

set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -std=c++11 -pthread")

# Set path for OpenCV if using it
if(UNITREE_CAMERA_USE_CUSTOM_OPENCV_DIR)
    set(OpenCV_DIR ${UNITREE_CAMERA_OPENCV_DIR}/lib/cmake/opencv4)

    # Add the manually specified path to the library path
    file(WRITE ${CMAKE_INSTALL_PREFIX}/share/${PROJECT_NAME}/opencv.bash
        "LD_LIBRARY_PATH=${UNITREE_CAMERA_OPENCV_DIR}/lib:$LD_LIBRARY_PATH"
    )
else()
    # Add the manually specified path to the library path
    file(WRITE ${CMAKE_INSTALL_PREFIX}/share/${PROJECT_NAME}/opencv.bash "" )
endif()

find_package(catkin REQUIRED COMPONENTS
  roscpp
  sensor_msgs
  cv_bridge
)

find_package(OpenCV 4 REQUIRED)
if(OpenCV_FOUND)
    include_directories(${OpenCV_INCLUDE_DIR})
    message(STATUS "OpenCV ${OpenCV_VERSION} FOUND")
    message(STATUS ${OpenCV_LIBS})
    if(${OpenCV_VERSION_MAJOR} EQUAL 4)
        add_definitions(-DOPENCV_VERSION_4)
    elseif(${OpenCV_VERSION_MAJOR} LESS 4)
        add_definitions(-DOPENCV_VERSION_3)
    endif()
else()
    message(FATAL_ERROR "OpenCV Library Not Found")
endif()

if(CMAKE_HOST_SYSTEM_PROCESSOR MATCHES "x86_64")
  set(LIBRARY_OUTPUT_PATH ${PROJECT_SOURCE_DIR}/lib/amd64/)
  link_directories(${PROJECT_SOURCE_DIR}/lib/amd64/)
else()
  set(LIBRARY_OUTPUT_PATH ${PROJECT_SOURCE_DIR}/lib/arm64/)
  link_directories(${PROJECT_SOURCE_DIR}/lib/arm64/)
endif()
include_directories(${OpenCV_INCLUDE_DIRS})
include_directories(${PROJECT_SOURCE_DIR}/include)
include_directories(${catkin_INCLUDE_DIRS})

set(SDKLIBS unitree_camera tstc_V4L2_xu_camera udev systemlog ${OpenCV_LIBS})

catkin_package(
  INCLUDE_DIRS include
  LIBRARIES ${PROJECT_NAME}
  CATKIN_DEPENDS roscpp sensor_msgs cv_bridge
)

file(WRITE ${CMAKE_CURRENT_BINARY_DIR}/null.cpp "")

add_library(UnitreeCameraSDK ${CMAKE_CURRENT_BINARY_DIR}/null.cpp)
target_link_libraries(${PROJECT_NAME}
  ${LIBRARY_OUTPUT_PATH}/libunitree_camera.a
  ${LIBRARY_OUTPUT_PATH}/libtstc_V4L2_xu_camera.a
  ${LIBRARY_OUTPUT_PATH}/libsystemlog.a
  udev
  ${OpenCV_LIBS}
)

add_subdirectory(${PROJECT_SOURCE_DIR}/examples)

install(DIRECTORY include/ DESTINATION include)
install(DIRECTORY ${LIBRARY_OUTPUT_PATH} DESTINATION lib)

install(TARGETS ${PROJECT_NAME}
        EXPORT ${PROJECT_NAME}-targets
        LIBRARY DESTINATION ${LIBRARY_OUTPUT_PATH}
        ARCHIVE DESTINATION ${LIBRARY_OUTPUT_PATH}
        INCLUDES DESTINATION include
)

# install(DIRECTORY ${LIBRARY_OUTPUT_PATH} DESTINATION ${CATKIN_PACKAGE_LIB_DESTINATION})

install(EXPORT ${PROJECT_NAME}-targets
        FILE ${PROJECT_NAME}-config.cmake
        NAMESPACE ${PROJECT_NAME}::
        DESTINATION lib/cmake/${PROJECT_NAME}
)
