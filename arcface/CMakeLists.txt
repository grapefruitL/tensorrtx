cmake_minimum_required(VERSION 2.6)

project(arcface)

add_definitions(-std=c++11)

option(CUDA_USE_STATIC_CUDA_RUNTIME OFF)
set(CMAKE_CXX_STANDARD 11)
set(CMAKE_BUILD_TYPE Debug)


set(CUDA_TOOLKIT_ROOT_DIR /home/dwk/thirdparty/cuda)
find_package(CUDA REQUIRED)
list(APPEND CUDA_NVCC_FLAGS "-std=c++11")

include_directories(/home/dwk/thirdparty/tensorRT/TensorRT-6.0.1.5.CentOS-7.6.x86_64-gnu.cuda-10.0.cudnn7.6/include)
link_directories(/home/dwk/thirdparty/tensorRT/TensorRT-6.0.1.5.CentOS-7.6.x86_64-gnu.cuda-10.0.cudnn7.6/lib)


include_directories(${PROJECT_SOURCE_DIR}/include)
if (CMAKE_SYSTEM_PROCESSOR MATCHES "aarch64")
    message("embed_platform on")
    include_directories(/home/dwk/thirdparty/cuda/targets/aarch64-linux/include)
    link_directories(/home/dwk/thirdparty/cuda/targets/aarch64-linux/lib)
else()
    message("embed_platform off")
    include_directories(/home/dwk/thirdparty/cuda/include)
    link_directories(/home/dwk/thirdparty/cuda/lib64)
endif()


set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -std=c++11 -Wall -Ofast -Wfatal-errors -D_MWAITXINTRIN_H_INCLUDED")

cuda_add_library(myplugins SHARED ${PROJECT_SOURCE_DIR}/prelu.cu)

set(OpenCV_DIR /home/dwk/thirdparty/opencv/v3.3.0-centos7.5/share/OpenCV)
find_package(OpenCV)
include_directories(${OpenCV_INCLUDE_DIRS})

# include_directories(/home/dwk/thirdparty/opencv/v3.3.0-centos7.5/include)
# link_directories(/home/dwk/thirdparty/opencv/v3.3.0-centos7.5/lib)

add_executable(arcface-r50 ${PROJECT_SOURCE_DIR}/arcface-r50.cpp)
target_link_libraries(arcface-r50 nvinfer)
target_link_libraries(arcface-r50 cudart)
target_link_libraries(arcface-r50 myplugins)
target_link_libraries(arcface-r50 ${OpenCV_LIBS})

add_definitions(-O2 -pthread)

