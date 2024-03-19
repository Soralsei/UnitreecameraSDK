find_package(OpenCV 4 REQUIRED)

include_directories(${OpenCV_INCLUDE_DIRS})
include_directories(${PROJECT_SOURCE_DIR}/include)
include_directories(${catkin_INCLUDE_DIRS})


message(STATUS ${OpenCV_LIBS})

if(CMAKE_HOST_SYSTEM_PROCESSOR MATCHES "x86_64")
  set(LIBRARY_OUTPUT_PATH ${PROJECT_SOURCE_DIR}/lib/amd64/)
  link_directories(${PROJECT_SOURCE_DIR}/lib/amd64/)
else()
  set(LIBRARY_OUTPUT_PATH ${PROJECT_SOURCE_DIR}/lib/arm64/)
  link_directories(${PROJECT_SOURCE_DIR}/lib/arm64/)
endif()

add_executable(example_getRawFrame ./example_getRawFrame.cc)
target_link_libraries(example_getRawFrame 
${LIBRARY_OUTPUT_PATH}/libunitree_camera.a
${LIBRARY_OUTPUT_PATH}/libtstc_V4L2_xu_camera.a
${LIBRARY_OUTPUT_PATH}/libsystemlog.a
udev
${OpenCV_LIBS}
)


add_executable(example_getDepthFrame ./example_getDepthFrame.cc)
target_link_libraries(example_getDepthFrame 
  ${LIBRARY_OUTPUT_PATH}/libunitree_camera.a
  ${LIBRARY_OUTPUT_PATH}/libtstc_V4L2_xu_camera.a
  ${LIBRARY_OUTPUT_PATH}/libsystemlog.a
  udev
  ${OpenCV_LIBS}
)

add_executable(example_getRectFrame ./example_getRectFrame.cc)
target_link_libraries(example_getRectFrame 
  ${LIBRARY_OUTPUT_PATH}/libunitree_camera.a
  ${LIBRARY_OUTPUT_PATH}/libtstc_V4L2_xu_camera.a
  ${LIBRARY_OUTPUT_PATH}/libsystemlog.a
  udev
  ${OpenCV_LIBS}
  )

add_executable(example_getCalibParamsFile ./example_getCalibParamsFile.cc)
target_link_libraries(example_getCalibParamsFile 
  ${LIBRARY_OUTPUT_PATH}/libunitree_camera.a
  ${LIBRARY_OUTPUT_PATH}/libtstc_V4L2_xu_camera.a
  ${LIBRARY_OUTPUT_PATH}/libsystemlog.a
  udev
  ${OpenCV_LIBS}
  )

add_executable(example_putImagetrans ./example_putImagetrans.cc)
target_link_libraries(example_putImagetrans 
  ${LIBRARY_OUTPUT_PATH}/libunitree_camera.a
  ${LIBRARY_OUTPUT_PATH}/libtstc_V4L2_xu_camera.a
  ${LIBRARY_OUTPUT_PATH}/libsystemlog.a
  udev
  ${OpenCV_LIBS}
  )

add_executable(example_getimagetrans ./example_getimagetrans.cc)
target_link_libraries(example_getimagetrans 
  ${LIBRARY_OUTPUT_PATH}/libunitree_camera.a
  ${LIBRARY_OUTPUT_PATH}/libtstc_V4L2_xu_camera.a
  ${LIBRARY_OUTPUT_PATH}/libsystemlog.a
  udev
  ${OpenCV_LIBS}
  )

# add_executable(example_share ./example_share.cc)
# target_link_libraries(example_share ${SDKLIBS})

# cmake_policy(SET CMP0072 NEW)
find_package(OpenGL REQUIRED)
if(OpenGL_FOUND)
    include_directories(${OPENGL_INCLUDE_DIR})
    message(STATUS ${OPENGL_INCLUDE_DIR})
    message(STATUS ${OPENGL_LIBRARIES})
else()
    message(WARNING "OpenGL Library Not Found")
endif()

find_package(GLUT REQUIRED)
if(GLUT_FOUND)
    include_directories(${GLUT_INCLUDE_DIR})
    message(STATUS ${GLUT_INCLUDE_DIR})
    message(STATUS ${GLUT_LIBRARY})
else()
    message(WARNING "GLUT Library Not Found")
endif()

find_package(X11 REQUIRED)
if(X11_FOUND)
    include_directories(${X11_INCLUDE_DIR})
    message(STATUS ${X11_INCLUDE_DIR})
    message(STATUS ${X11_LIBRARIES})
else()
    message(WARNING "X11 Library Not Found")
endif()

if(X11_FOUND AND OpenGL_FOUND AND GLUT_FOUND)
    set(ShowPointCloud true)
    message(STATUS "Point Cloud Example Enabled")
else()
    set(ShowPointCloud false)
    message(WARNING "Point Cloud Example Disabled")
endif()

if(${ShowPointCloud})
add_executable(example_getPointCloud ./example_getPointCloud.cc ./glViewer/glwindow_x11.cpp ./glViewer/scenewindow.cpp)
target_link_libraries(example_getPointCloud 
  ${LIBRARY_OUTPUT_PATH}/libunitree_camera.a
  ${LIBRARY_OUTPUT_PATH}/libtstc_V4L2_xu_camera.a
  ${LIBRARY_OUTPUT_PATH}/libsystemlog.a
  udev ${OpenCV_LIBS} ${OPENGL_LIBRARIES} ${GLUT_LIBRARY} ${X11_LIBRARIES} )
endif()


# set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -std=c++11 -pthread")


