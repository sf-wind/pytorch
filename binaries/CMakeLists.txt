if(INTERN_BUILD_MOBILE)
  caffe2_binary_target("speed_benchmark_torch.cc")
  caffe2_binary_target("load_benchmark_torch.cc")
  if(NOT BUILD_LITE_INTERPRETER)
    caffe2_binary_target("compare_models_torch.cc")
  endif()
  return()
endif()

if(BUILD_CAFFE2)
  caffe2_binary_target("at_launch_benchmark.cc")
  target_include_directories(at_launch_benchmark PUBLIC
    ${CMAKE_BINARY_DIR}/aten/src)

  caffe2_binary_target("intra_inter_benchmark.cc")
  target_include_directories(intra_inter_benchmark PUBLIC
    ${CMAKE_BINARY_DIR}/aten/src)
endif()

caffe2_binary_target("parallel_info.cc")
target_include_directories(parallel_info PUBLIC
  ${CMAKE_BINARY_DIR}/aten/src) # provides "ATen/TypeExtendedInterface.h" to ATen.h

caffe2_binary_target("record_function_benchmark.cc")
target_include_directories(record_function_benchmark PUBLIC
  ${CMAKE_BINARY_DIR}/aten/src)

caffe2_binary_target("speed_benchmark_torch.cc")
caffe2_binary_target("compare_models_torch.cc")

if(BUILD_TEST)
  # Core overhead benchmark
  caffe2_binary_target("core_overhead_benchmark.cc")
  target_link_libraries(core_overhead_benchmark benchmark)
endif()

if(USE_CUDA)
  caffe2_binary_target("inspect_gpu.cc")
  target_link_libraries(inspect_gpu ${CUDA_LIBRARIES})
  caffe2_binary_target("print_core_object_sizes_gpu.cc")

  if(BUILD_TEST)
    # Core overhead benchmark
    caffe2_binary_target("core_overhead_benchmark_gpu.cc")
    target_link_libraries(core_overhead_benchmark_gpu benchmark ${CUDA_curand_LIBRARY})
  endif()
endif()

if(USE_ROCM)
  caffe2_hip_binary_target("hip/inspect_gpu.cc")
  caffe2_hip_binary_target("hip/print_core_object_sizes_gpu.cc")

  if(BUILD_TEST)
    # Core overhead benchmark
    caffe2_hip_binary_target("hip/core_overhead_benchmark_gpu.cc")
    target_link_libraries(core_overhead_benchmark_gpu benchmark)
  endif()
endif()

if(USE_MPI)
  caffe2_binary_target("run_plan_mpi.cc")
  target_link_libraries(run_plan_mpi ${MPI_CXX_LIBRARIES})
endif()


caffe2_binary_target("dump_operator_names.cc")
caffe2_binary_target("optimize_for_mobile.cc")

caffe2_binary_target(aot_model_compiler "aot_model_compiler.cc")
target_link_libraries(aot_model_compiler torch)
