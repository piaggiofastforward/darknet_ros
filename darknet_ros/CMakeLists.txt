cmake_minimum_required(VERSION 3.5.1)
project(darknet_ros)

# Set c++11 cmake flags
set(CMAKE_CXX_STANDARD 11)
set(CMAKE_C_FLAGS "-Wall -Wno-unused-result -Wno-unknown-pragmas -Wno-unused-variable -Wfatal-errors -fPIC ${CMAKE_C_FLAGS}")
set(CMAKE_EXPORT_COMPILE_COMMANDS ON)
set(CMAKE_BUILD_WITH_INSTALL_RPATH TRUE)
set(CMAKE_INSTALL_RPATH "/usr/local/pff_opencv/lib")

# Define path of darknet folder here.
find_path(DARKNET_PATH
  NAMES "README.md"
  HINTS "${CMAKE_CURRENT_SOURCE_DIR}/../darknet/")
message(STATUS "Darknet path dir = ${DARKNET_PATH}")
add_definitions(-DDARKNET_FILE_PATH="${DARKNET_PATH}")

# Find CUDA
find_package(CUDA QUIET)
if (CUDA_FOUND)
  find_package(CUDA REQUIRED)
  message(STATUS "CUDA Version: ${CUDA_VERSION_STRINGS}")
  message(STATUS "CUDA Libararies: ${CUDA_LIBRARIES}")
  set(
    CUDA_NVCC_FLAGS
    ${CUDA_NVCC_FLAGS};
    -O3
    #-gencode arch=compute_62,code=sm_62
    #-gencode arch=compute_75,code=sm_75
    -gencode arch=compute_80,code=sm_80
  )
  add_definitions(-DGPU)
else()
  list(APPEND LIBRARIES "m")
endif()

# Find X11
message ( STATUS "Searching for X11..." )
find_package ( X11 REQUIRED )
if ( X11_FOUND )
  include_directories ( ${X11_INCLUDE_DIR} )
  link_libraries ( ${X11_LIBRARIES} )
  message ( STATUS " X11_INCLUDE_DIR: " ${X11_INCLUDE_DIR} )
  message ( STATUS " X11_LIBRARIES: " ${X11_LIBRARIES} )
endif ( X11_FOUND )

# Find rquired packeges
find_package(Boost REQUIRED COMPONENTS thread)

# Manually set opencv paths
set(OpenCV_DIR "/usr/local/pff_opencv")
set(OpenCV_INCLUDE_DIRS "/usr/local/pff_opencv/include")
set(OpenCV_LIB_DIR "/usr/local/pff_opencv/lib")

# Include OpenCV directories
include_directories(${OpenCV_INCLUDE_DIRS})
# Link to OpenCV libraries
link_directories(${OpenCV_LIB_DIR})
# Manually Specify Opencv libraries
set(OpenCV_LIBS
  ${OpenCV_LIB_DIR}/libopencv_core.so
  ${OpenCV_LIB_DIR}/libopencv_core.so.4.2
  ${OpenCV_LIB_DIR}/libopencv_core.so.4.2.0
  ${OpenCV_LIB_DIR}/libopencv_aruco.so
  ${OpenCV_LIB_DIR}/libopencv_aruco.so.4.2
  ${OpenCV_LIB_DIR}/libopencv_aruco.so.4.2.0
  ${OpenCV_LIB_DIR}/libopencv_bgsegm.so
  ${OpenCV_LIB_DIR}/libopencv_bgsegm.so.4.2
  ${OpenCV_LIB_DIR}/libopencv_bgsegm.so.4.2.0
  ${OpenCV_LIB_DIR}/libopencv_bioinspired.so
  ${OpenCV_LIB_DIR}/libopencv_bioinspired.so.4.2
  ${OpenCV_LIB_DIR}/libopencv_bioinspired.so.4.2.0
  ${OpenCV_LIB_DIR}/libopencv_calib3d.so
  ${OpenCV_LIB_DIR}/libopencv_calib3d.so.4.2
  ${OpenCV_LIB_DIR}/libopencv_calib3d.so.4.2.0
  ${OpenCV_LIB_DIR}/libopencv_ccalib.so
  ${OpenCV_LIB_DIR}/libopencv_ccalib.so.4.2
  ${OpenCV_LIB_DIR}/libopencv_ccalib.so.4.2.0
  ${OpenCV_LIB_DIR}/libopencv_cudaarithm.so
  ${OpenCV_LIB_DIR}/libopencv_cudaarithm.so.4.2
  ${OpenCV_LIB_DIR}/libopencv_cudaarithm.so.4.2.0
  ${OpenCV_LIB_DIR}/libopencv_cudabgsegm.so
  ${OpenCV_LIB_DIR}/libopencv_cudabgsegm.so.4.2
  ${OpenCV_LIB_DIR}/libopencv_cudabgsegm.so.4.2.0
  ${OpenCV_LIB_DIR}/libopencv_cudacodec.so
  ${OpenCV_LIB_DIR}/libopencv_cudacodec.so.4.2
  ${OpenCV_LIB_DIR}/libopencv_cudacodec.so.4.2.0
  ${OpenCV_LIB_DIR}/libopencv_cudafeatures2d.so
  ${OpenCV_LIB_DIR}/libopencv_cudafeatures2d.so.4.2
  ${OpenCV_LIB_DIR}/libopencv_cudafeatures2d.so.4.2.0
  ${OpenCV_LIB_DIR}/libopencv_cudafilters.so
  ${OpenCV_LIB_DIR}/libopencv_cudafilters.so.4.2
  ${OpenCV_LIB_DIR}/libopencv_cudafilters.so.4.2.0
  ${OpenCV_LIB_DIR}/libopencv_cudaimgproc.so
  ${OpenCV_LIB_DIR}/libopencv_cudaimgproc.so.4.2
  ${OpenCV_LIB_DIR}/libopencv_cudaimgproc.so.4.2.0
  ${OpenCV_LIB_DIR}/libopencv_cudalegacy.so
  ${OpenCV_LIB_DIR}/libopencv_cudalegacy.so.4.2
  ${OpenCV_LIB_DIR}/libopencv_cudalegacy.so.4.2.0
  ${OpenCV_LIB_DIR}/libopencv_cudaobjdetect.so
  ${OpenCV_LIB_DIR}/libopencv_cudaobjdetect.so.4.2
  ${OpenCV_LIB_DIR}/libopencv_cudaobjdetect.so.4.2.0
  ${OpenCV_LIB_DIR}/libopencv_cudaoptflow.so
  ${OpenCV_LIB_DIR}/libopencv_cudaoptflow.so.4.2
  ${OpenCV_LIB_DIR}/libopencv_cudaoptflow.so.4.2.0
  ${OpenCV_LIB_DIR}/libopencv_cudastereo.so
  ${OpenCV_LIB_DIR}/libopencv_cudastereo.so.4.2
  ${OpenCV_LIB_DIR}/libopencv_cudastereo.so.4.2.0
  ${OpenCV_LIB_DIR}/libopencv_cudawarping.so
  ${OpenCV_LIB_DIR}/libopencv_cudawarping.so.4.2
  ${OpenCV_LIB_DIR}/libopencv_cudawarping.so.4.2.0
  ${OpenCV_LIB_DIR}/libopencv_cudev.so
  ${OpenCV_LIB_DIR}/libopencv_cudev.so.4.2
  ${OpenCV_LIB_DIR}/libopencv_cudev.so.4.2.0
  ${OpenCV_LIB_DIR}/libopencv_datasets.so
  ${OpenCV_LIB_DIR}/libopencv_datasets.so.4.2
  ${OpenCV_LIB_DIR}/libopencv_datasets.so.4.2.0
  ${OpenCV_LIB_DIR}/libopencv_dnn.so
  ${OpenCV_LIB_DIR}/libopencv_dnn.so.4.2
  ${OpenCV_LIB_DIR}/libopencv_dnn.so.4.2.0
  ${OpenCV_LIB_DIR}/libopencv_dnn_superres.so
  ${OpenCV_LIB_DIR}/libopencv_dnn_superres.so.4.2
  ${OpenCV_LIB_DIR}/libopencv_dnn_superres.so.4.2.0
  ${OpenCV_LIB_DIR}/libopencv_dpm.so
  ${OpenCV_LIB_DIR}/libopencv_dpm.so.4.2
  ${OpenCV_LIB_DIR}/libopencv_dpm.so.4.2.0
  ${OpenCV_LIB_DIR}/libopencv_face.so
  ${OpenCV_LIB_DIR}/libopencv_face.so.4.2
  ${OpenCV_LIB_DIR}/libopencv_face.so.4.2.0
  ${OpenCV_LIB_DIR}/libopencv_features2d.so
  ${OpenCV_LIB_DIR}/libopencv_features2d.so.4.2
  ${OpenCV_LIB_DIR}/libopencv_features2d.so.4.2.0
  ${OpenCV_LIB_DIR}/libopencv_flann.so
  ${OpenCV_LIB_DIR}/libopencv_flann.so.4.2
  ${OpenCV_LIB_DIR}/libopencv_flann.so.4.2.0
  ${OpenCV_LIB_DIR}/libopencv_freetype.so
  ${OpenCV_LIB_DIR}/libopencv_freetype.so.4.2
  ${OpenCV_LIB_DIR}/libopencv_freetype.so.4.2.0
  ${OpenCV_LIB_DIR}/libopencv_fuzzy.so
  ${OpenCV_LIB_DIR}/libopencv_fuzzy.so.4.2
  ${OpenCV_LIB_DIR}/libopencv_fuzzy.so.4.2.0
  ${OpenCV_LIB_DIR}/libopencv_gapi.so
  ${OpenCV_LIB_DIR}/libopencv_gapi.so.4.2
  ${OpenCV_LIB_DIR}/libopencv_gapi.so.4.2.0
  ${OpenCV_LIB_DIR}/libopencv_hfs.so
  ${OpenCV_LIB_DIR}/libopencv_hfs.so.4.2
  ${OpenCV_LIB_DIR}/libopencv_hfs.so.4.2.0
  ${OpenCV_LIB_DIR}/libopencv_highgui.so
  ${OpenCV_LIB_DIR}/libopencv_highgui.so.4.2
  ${OpenCV_LIB_DIR}/libopencv_highgui.so.4.2.0
  ${OpenCV_LIB_DIR}/libopencv_imgcodecs.so
  ${OpenCV_LIB_DIR}/libopencv_imgcodecs.so.4.2
  ${OpenCV_LIB_DIR}/libopencv_imgcodecs.so.4.2.0
  ${OpenCV_LIB_DIR}/libopencv_img_hash.so
  ${OpenCV_LIB_DIR}/libopencv_img_hash.so.4.2
  ${OpenCV_LIB_DIR}/libopencv_img_hash.so.4.2.0
  ${OpenCV_LIB_DIR}/libopencv_imgproc.so
  ${OpenCV_LIB_DIR}/libopencv_imgproc.so.4.2
  ${OpenCV_LIB_DIR}/libopencv_imgproc.so.4.2.0
  ${OpenCV_LIB_DIR}/libopencv_line_descriptor.so
  ${OpenCV_LIB_DIR}/libopencv_line_descriptor.so.4.2
  ${OpenCV_LIB_DIR}/libopencv_line_descriptor.so.4.2.0
  ${OpenCV_LIB_DIR}/libopencv_ml.so
  ${OpenCV_LIB_DIR}/libopencv_ml.so.4.2
  ${OpenCV_LIB_DIR}/libopencv_ml.so.4.2.0
  ${OpenCV_LIB_DIR}/libopencv_objdetect.so
  ${OpenCV_LIB_DIR}/libopencv_objdetect.so.4.2
  ${OpenCV_LIB_DIR}/libopencv_objdetect.so.4.2.0
  ${OpenCV_LIB_DIR}/libopencv_optflow.so
  ${OpenCV_LIB_DIR}/libopencv_optflow.so.4.2
  ${OpenCV_LIB_DIR}/libopencv_optflow.so.4.2.0
  ${OpenCV_LIB_DIR}/libopencv_phase_unwrapping.so
  ${OpenCV_LIB_DIR}/libopencv_phase_unwrapping.so.4.2
  ${OpenCV_LIB_DIR}/libopencv_phase_unwrapping.so.4.2.0
  ${OpenCV_LIB_DIR}/libopencv_photo.so
  ${OpenCV_LIB_DIR}/libopencv_photo.so.4.2
  ${OpenCV_LIB_DIR}/libopencv_photo.so.4.2.0
  ${OpenCV_LIB_DIR}/libopencv_plot.so
  ${OpenCV_LIB_DIR}/libopencv_plot.so.4.2
  ${OpenCV_LIB_DIR}/libopencv_plot.so.4.2.0
  ${OpenCV_LIB_DIR}/libopencv_reg.so
  ${OpenCV_LIB_DIR}/libopencv_reg.so.4.2
  ${OpenCV_LIB_DIR}/libopencv_reg.so.4.2.0
  ${OpenCV_LIB_DIR}/libopencv_rgbd.so
  ${OpenCV_LIB_DIR}/libopencv_rgbd.so.4.2
  ${OpenCV_LIB_DIR}/libopencv_rgbd.so.4.2.0
  ${OpenCV_LIB_DIR}/libopencv_saliency.so
  ${OpenCV_LIB_DIR}/libopencv_saliency.so.4.2
  ${OpenCV_LIB_DIR}/libopencv_saliency.so.4.2.0
  ${OpenCV_LIB_DIR}/libopencv_shape.so
  ${OpenCV_LIB_DIR}/libopencv_shape.so.4.2
  ${OpenCV_LIB_DIR}/libopencv_shape.so.4.2.0
  ${OpenCV_LIB_DIR}/libopencv_stereo.so
  ${OpenCV_LIB_DIR}/libopencv_stereo.so.4.2
  ${OpenCV_LIB_DIR}/libopencv_stereo.so.4.2.0
  ${OpenCV_LIB_DIR}/libopencv_stitching.so
  ${OpenCV_LIB_DIR}/libopencv_stitching.so.4.2
  ${OpenCV_LIB_DIR}/libopencv_stitching.so.4.2.0
  ${OpenCV_LIB_DIR}/libopencv_structured_light.so
  ${OpenCV_LIB_DIR}/libopencv_structured_light.so.4.2
  ${OpenCV_LIB_DIR}/libopencv_structured_light.so.4.2.0
  ${OpenCV_LIB_DIR}/libopencv_surface_matching.so
  ${OpenCV_LIB_DIR}/libopencv_surface_matching.so.4.2
  ${OpenCV_LIB_DIR}/libopencv_surface_matching.so.4.2.0
  ${OpenCV_LIB_DIR}/libopencv_tracking.so
  ${OpenCV_LIB_DIR}/libopencv_tracking.so.4.2
  ${OpenCV_LIB_DIR}/libopencv_tracking.so.4.2.0
  ${OpenCV_LIB_DIR}/libopencv_video.so
  ${OpenCV_LIB_DIR}/libopencv_video.so.4.2
  ${OpenCV_LIB_DIR}/libopencv_video.so.4.2.0
  ${OpenCV_LIB_DIR}/libopencv_videoio.so
  ${OpenCV_LIB_DIR}/libopencv_videoio.so.4.2
  ${OpenCV_LIB_DIR}/libopencv_videoio.so.4.2.0
  ${OpenCV_LIB_DIR}/libopencv_videostab.so
  ${OpenCV_LIB_DIR}/libopencv_videostab.so.4.2
  ${OpenCV_LIB_DIR}/libopencv_videostab.so.4.2.0
  ${OpenCV_LIB_DIR}/libopencv_xfeatures2d.so
  ${OpenCV_LIB_DIR}/libopencv_xfeatures2d.so.4.2
  ${OpenCV_LIB_DIR}/libopencv_xfeatures2d.so.4.2.0
  ${OpenCV_LIB_DIR}/libopencv_ximgproc.so
  ${OpenCV_LIB_DIR}/libopencv_ximgproc.so.4.2
  ${OpenCV_LIB_DIR}/libopencv_ximgproc.so.4.2.0
  ${OpenCV_LIB_DIR}/libopencv_xobjdetect.so
  ${OpenCV_LIB_DIR}/libopencv_xobjdetect.so.4.2
  ${OpenCV_LIB_DIR}/libopencv_xobjdetect.so.4.2.0
  ${OpenCV_LIB_DIR}/libopencv_xphoto.so
  ${OpenCV_LIB_DIR}/libopencv_xphoto.so.4.2
  ${OpenCV_LIB_DIR}/libopencv_xphoto.so.4.2.0
)




#find_package(OpenCV REQUIRED)
#include_directories(${OpenCV_INCLUDE_DIRS})
find_package(catkin REQUIRED
  COMPONENTS
    cv_bridge
    roscpp
    rospy
    std_msgs
    actionlib
    darknet_ros_msgs
    image_transport
    nodelet
)

# Enable OPENCV in darknet
add_definitions(-DOPENCV)
add_definitions(-O4 -g)

catkin_package(
  INCLUDE_DIRS
    include
  LIBRARIES
    ${PROJECT_NAME}_lib
  CATKIN_DEPENDS
    cv_bridge
    roscpp
    actionlib
    rospy
    std_msgs
    darknet_ros_msgs
    image_transport
    nodelet
  DEPENDS
    Boost
)

include_directories(
  ${DARKNET_PATH}/src
  ${DARKNET_PATH}/include
  include
  ${Boost_INCLUDE_DIRS}
  ${catkin_INCLUDE_DIRS}
)

set(PROJECT_LIB_FILES
    src/YoloObjectDetector.cpp                    src/image_interface.cpp
)

set(DARKNET_CORE_FILES
    ${DARKNET_PATH}/src/activation_layer.c        ${DARKNET_PATH}/src/im2col.c
    ${DARKNET_PATH}/src/activations.c             ${DARKNET_PATH}/src/image.c
    ${DARKNET_PATH}/src/avgpool_layer.c           ${DARKNET_PATH}/src/layer.c
    ${DARKNET_PATH}/src/batchnorm_layer.c         ${DARKNET_PATH}/src/list.c
    ${DARKNET_PATH}/src/blas.c                    ${DARKNET_PATH}/src/local_layer.c
    ${DARKNET_PATH}/src/box.c                     ${DARKNET_PATH}/src/lstm_layer.c
    ${DARKNET_PATH}/src/col2im.c                  ${DARKNET_PATH}/src/matrix.c
    ${DARKNET_PATH}/src/connected_layer.c         ${DARKNET_PATH}/src/maxpool_layer.c
    ${DARKNET_PATH}/src/convolutional_layer.c     ${DARKNET_PATH}/src/network.c
    ${DARKNET_PATH}/src/cost_layer.c              ${DARKNET_PATH}/src/normalization_layer.c
    ${DARKNET_PATH}/src/crnn_layer.c              ${DARKNET_PATH}/src/option_list.c
    ${DARKNET_PATH}/src/crop_layer.c              ${DARKNET_PATH}/src/parser.c
    ${DARKNET_PATH}/src/cuda.c                    ${DARKNET_PATH}/src/region_layer.c
    ${DARKNET_PATH}/src/data.c                    ${DARKNET_PATH}/src/reorg_layer.c
    ${DARKNET_PATH}/src/deconvolutional_layer.c   ${DARKNET_PATH}/src/rnn_layer.c
    ${DARKNET_PATH}/src/demo.c                    ${DARKNET_PATH}/src/route_layer.c
    ${DARKNET_PATH}/src/detection_layer.c         ${DARKNET_PATH}/src/shortcut_layer.c
    ${DARKNET_PATH}/src/dropout_layer.c           ${DARKNET_PATH}/src/softmax_layer.c
    ${DARKNET_PATH}/src/gemm.c                    ${DARKNET_PATH}/src/tree.c
    ${DARKNET_PATH}/src/gru_layer.c               ${DARKNET_PATH}/src/utils.c
    ${DARKNET_PATH}/src/upsample_layer.c          ${DARKNET_PATH}/src/logistic_layer.c
    ${DARKNET_PATH}/src/l2norm_layer.c            ${DARKNET_PATH}/src/yolo_layer.c
    ${DARKNET_PATH}/src/iseg_layer.c              ${DARKNET_PATH}/src/image_opencv.cpp

    ${DARKNET_PATH}/examples/art.c                ${DARKNET_PATH}/examples/lsd.c
    ${DARKNET_PATH}/examples/nightmare.c          ${DARKNET_PATH}/examples/instance-segmenter.c
    ${DARKNET_PATH}/examples/captcha.c            ${DARKNET_PATH}/examples/regressor.c
    ${DARKNET_PATH}/examples/cifar.c              ${DARKNET_PATH}/examples/rnn.c
    ${DARKNET_PATH}/examples/classifier.c         ${DARKNET_PATH}/examples/segmenter.c
    ${DARKNET_PATH}/examples/coco.c               ${DARKNET_PATH}/examples/super.c
    ${DARKNET_PATH}/examples/darknet.c            ${DARKNET_PATH}/examples/tag.c
    ${DARKNET_PATH}/examples/detector.c           ${DARKNET_PATH}/examples/yolo.c
    ${DARKNET_PATH}/examples/go.c
)

set(DARKNET_CUDA_FILES
    ${DARKNET_PATH}/src/activation_kernels.cu     ${DARKNET_PATH}/src/crop_layer_kernels.cu
    ${DARKNET_PATH}/src/avgpool_layer_kernels.cu  ${DARKNET_PATH}/src/deconvolutional_kernels.cu
    ${DARKNET_PATH}/src/blas_kernels.cu           ${DARKNET_PATH}/src/dropout_layer_kernels.cu
    ${DARKNET_PATH}/src/col2im_kernels.cu         ${DARKNET_PATH}/src/im2col_kernels.cu
    ${DARKNET_PATH}/src/convolutional_kernels.cu  ${DARKNET_PATH}/src/maxpool_layer_kernels.cu
)

if (CUDA_FOUND)

  link_directories(
    ${CUDA_TOOLKIT_ROOT_DIR}/lib64
  )

  cuda_add_library(${PROJECT_NAME}_lib
    ${PROJECT_LIB_FILES} ${DARKNET_CORE_FILES}
    ${DARKNET_CUDA_FILES}
  )

  target_link_libraries(${PROJECT_NAME}_lib
    cuda
    cudart
    cublas
    curand
  )

  cuda_add_executable(${PROJECT_NAME}
    src/yolo_object_detector_node.cpp
  )

  cuda_add_library(${PROJECT_NAME}_nodelet
    src/yolo_object_detector_nodelet.cpp
  )

else()

  add_library(${PROJECT_NAME}_lib
    ${PROJECT_LIB_FILES} ${DARKNET_CORE_FILES}
  )

  add_executable(${PROJECT_NAME}
    src/yolo_object_detector_node.cpp
  )

  add_library(${PROJECT_NAME}_nodelet
    src/yolo_object_detector_nodelet.cpp
  )

endif()

target_link_libraries(${PROJECT_NAME}_lib
  m
  pthread
  stdc++
  ${Boost_LIBRARIES}
  ${OpenCV_LIBRARIES}
  ${catkin_LIBRARIES}
  ${OpenCV_LIBS}
)

target_link_libraries(${PROJECT_NAME}
  ${PROJECT_NAME}_lib
)

target_link_libraries(${PROJECT_NAME}_nodelet
  ${PROJECT_NAME}_lib
)

add_dependencies(${PROJECT_NAME}_lib
  darknet_ros_msgs_generate_messages_cpp
)

install(TARGETS ${PROJECT_NAME}_lib
  ARCHIVE DESTINATION ${CATKIN_PACKAGE_LIB_DESTINATION}
  LIBRARY DESTINATION ${CATKIN_PACKAGE_LIB_DESTINATION}
  RUNTIME DESTINATION ${CATKIN_PACKAGE_BIN_DESTINATION}
)

install(TARGETS ${PROJECT_NAME}
  RUNTIME DESTINATION ${CATKIN_PACKAGE_BIN_DESTINATION}
)

install(
  DIRECTORY include/${PROJECT_NAME}/
  DESTINATION ${CATKIN_PACKAGE_INCLUDE_DESTINATION}
  FILES_MATCHING PATTERN "*.h"
)

install(DIRECTORY config launch yolo_network_config
  DESTINATION ${CATKIN_PACKAGE_SHARE_DESTINATION}
)

# Download yolov2-tiny.weights
set(PATH "${CMAKE_CURRENT_SOURCE_DIR}/yolo_network_config/weights")
set(FILE "${PATH}/yolov2-tiny.weights")
message(STATUS "Checking and downloading yolov2-tiny.weights if needed ...")
if (NOT EXISTS "${FILE}")
  message(STATUS "... file does not exist. Downloading now ...")
  execute_process(COMMAND wget -q https://github.com/leggedrobotics/darknet_ros/releases/download/1.1.4/yolov2-tiny.weights -P ${PATH})
endif()

# Download yolov3.weights
set(FILE "${PATH}/yolov3.weights")
message(STATUS "Checking and downloading yolov3.weights if needed ...")
if (NOT EXISTS "${FILE}")
  message(STATUS "... file does not exist. Downloading now ...")
  execute_process(COMMAND wget -q https://github.com/leggedrobotics/darknet_ros/releases/download/1.1.4/yolov3.weights -P ${PATH})
endif()

#############
## Testing ##
#############

if(CATKIN_ENABLE_TESTING)
  # Download yolov2.weights
  set(PATH "${CMAKE_CURRENT_SOURCE_DIR}/yolo_network_config/weights")
  set(FILE "${PATH}/yolov2.weights")
  message(STATUS "Checking and downloading yolov2.weights if needed ...")
  if (NOT EXISTS "${FILE}")
    message(STATUS "... file does not exist. Downloading now ...")
    execute_process(COMMAND wget -q https://github.com/leggedrobotics/darknet_ros/releases/download/1.1.4/yolov2.weights -P ${PATH})
  endif()

  find_package(rostest REQUIRED)

  # Object detection in images.
  add_rostest_gtest(${PROJECT_NAME}_object_detection-test
    test/object_detection.test
    test/test_main.cpp
    test/ObjectDetection.cpp
  )
  target_link_libraries(${PROJECT_NAME}_object_detection-test
    ${catkin_LIBRARIES}
  )
endif()

#########################
###   CLANG TOOLING   ###
#########################
find_package(cmake_clang_tools QUIET)
if (cmake_clang_tools_FOUND)
  message(STATUS "Run clang tooling")
  add_clang_tooling(
    TARGETS ${PROJECT_NAME}
    SOURCE_DIRS ${CMAKE_CURRENT_LIST_DIR}/src ${CMAKE_CURRENT_LIST_DIR}/include ${CMAKE_CURRENT_LIST_DIR}/test
    CT_HEADER_DIRS ${CMAKE_CURRENT_LIST_DIR}/include
    CF_WERROR
  )
endif (cmake_clang_tools_FOUND)

