# CMake file for MST test

# set(CUDA_NVCC_FLAGS ${CUDA_NVCC_FLAGS} -g;-G)

if(mgpu_INCLUDE_DIRS)
  include_directories(${mgpu_INCLUDE_DIRS})
else()
  message(SEND_ERROR "Modern GPU include directory not set.")
endif()

set (mgpu_SOURCE_FILES
  ${mgpu_SOURCE_DIRS}/mgpucontext.cu
  ${mgpu_SOURCE_DIRS}/mgpuutil.cpp)

if (cub_INCLUDE_DIRS)
  include_directories(${cub_INCLUDE_DIRS})
else()
  message(SEND_ERROR "CUB include directory not set.")
endif()

CUDA_ADD_EXECUTABLE(minimum_spanning_tree
  test_mst.cu
  ${CMAKE_SOURCE_DIR}/gunrock/util/test_utils.cu
  ${CMAKE_SOURCE_DIR}/gunrock/util/error_utils.cu
  ${CMAKE_SOURCE_DIR}/gunrock/util/misc_utils.cu
  ${CMAKE_SOURCE_DIR}/gunrock/util/gitsha1.c
  ${mgpu_SOURCE_FILES}
  OPTIONS ${GENCODE} ${VERBOSE_PTXAS})

target_link_libraries(minimum_spanning_tree ${Boost_LIBRARIES})
target_link_libraries(minimum_spanning_tree ${METIS_LIBRARY})

# OpenMP on OS X/clang
if(${CMAKE_SYSTEM_NAME} MATCHES "Darwin")
  if ("${CMAKE_CXX_COMPILER_ID}" STREQUAL "Clang")
    link_directories("/opt/local/lib")
    target_link_libraries(minimum_spanning_tree "omp")
  endif ("${CMAKE_CXX_COMPILER_ID}" STREQUAL "Clang")
endif(${CMAKE_SYSTEM_NAME} MATCHES "Darwin")
