add_subdirectory(adt)
add_subdirectory(symbolic)
add_subdirectory(inference)
add_subdirectory(sub_graphs)
add_subdirectory(performance)

if(WITH_GPU)
  file(
    GLOB CINN_PIR_TEST
    RELATIVE "${CMAKE_CURRENT_SOURCE_DIR}"
    "test_*.py")
  string(REPLACE ".py" "" CINN_PIR_TEST "${CINN_PIR_TEST}")

  list(REMOVE_ITEM CINN_PIR_TEST test_subgraph_checker)
  foreach(cinn_pir_test_name ${CINN_PIR_TEST})
    add_test(
      NAME ${cinn_pir_test_name}
      COMMAND
        ${CMAKE_COMMAND} -E env
        PYTHONPATH=${CMAKE_BINARY_DIR}:${CMAKE_BINARY_DIR}/python/:$ENV{PYTHONPATH}
        FLAGS_enable_pir_api=1 FLAGS_prim_all=True FLAGS_check_infer_symbolic=1
        FLAGS_cinn_new_group_scheduler=1 FLAGS_cinn_bucket_compile=1
        FLAGS_group_schedule_tiling_first=1 ${PYTHON_EXECUTABLE}
        ${CMAKE_CURRENT_SOURCE_DIR}/${cinn_pir_test_name}.py
      WORKING_DIRECTORY ${CMAKE_BINARY_DIR})
    set_tests_properties(${cinn_pir_test_name} PROPERTIES LABELS
                                                          "RUN_TYPE=CINN")
  endforeach()

  add_test(
    NAME test_cinn_sub_graph_stride_read
    COMMAND
      ${CMAKE_COMMAND} -E env
      PYTHONPATH=${CMAKE_BINARY_DIR}:${CMAKE_BINARY_DIR}/python/:$ENV{PYTHONPATH}
      FLAGS_enable_pir_api=1 FLAGS_prim_all=True
      FLAGS_cinn_new_group_scheduler=1 FLAGS_cinn_bucket_compile=1
      FLAGS_group_schedule_tiling_first=1 ${PYTHON_EXECUTABLE}
      ${CMAKE_CURRENT_SOURCE_DIR}/test_cinn_sub_graph.py
    WORKING_DIRECTORY ${CMAKE_BINARY_DIR})
  set_tests_properties(test_cinn_sub_graph_stride_read
                       PROPERTIES LABELS "RUN_TYPE=CINN")

  # add_test(
  #   NAME test_subgraph_checker
  #   COMMAND
  #     ${CMAKE_COMMAND} -E env
  #     PYTHONPATH=${CMAKE_BINARY_DIR}:${CMAKE_BINARY_DIR}/python/:$ENV{PYTHONPATH}
  #     FLAGS_enable_pir_api=1 FLAGS_prim_all=True
  #     FLAGS_cinn_new_group_scheduler=1 FLAGS_cinn_bucket_compile=1
  #     FLAGS_group_schedule_tiling_first=1
  #     ${PYTHON_EXECUTABLE}
  #     ${CMAKE_CURRENT_SOURCE_DIR}/test_subgraph_checker.py
  #   WORKING_DIRECTORY ${CMAKE_BINARY_DIR})
  # set_tests_properties(test_subgraph_checker PROPERTIES LABELS "RUN_TYPE=CINN")
  add_test(
    NAME test_rms_norm_seq_len_symbolic
    COMMAND
      ${CMAKE_COMMAND} -E env
      PYTHONPATH=${CMAKE_BINARY_DIR}:${CMAKE_BINARY_DIR}/python/:$ENV{PYTHONPATH}
      FLAGS_cinn_convert_static_dim_to_dynamic_dim=2048:S0
      FLAGS_group_schedule_tiling_first=1 FLAGS_cinn_bucket_compile=True
      FLAGS_enable_pir_api=1 ${PYTHON_EXECUTABLE}
      ${CMAKE_CURRENT_SOURCE_DIR}/test_rms_norm.py
    WORKING_DIRECTORY ${CMAKE_BINARY_DIR})
  set_tests_properties(test_rms_norm_seq_len_symbolic
                       PROPERTIES LABELS "RUN_TYPE=CINN")
  add_test(
    NAME test_rms_norm_bs_symbolic
    COMMAND
      ${CMAKE_COMMAND} -E env
      PYTHONPATH=${CMAKE_BINARY_DIR}:${CMAKE_BINARY_DIR}/python/:$ENV{PYTHONPATH}
      FLAGS_cinn_convert_static_dim_to_dynamic_dim=7:S1
      FLAGS_group_schedule_tiling_first=1 FLAGS_cinn_bucket_compile=True
      FLAGS_enable_pir_api=1 ${PYTHON_EXECUTABLE}
      ${CMAKE_CURRENT_SOURCE_DIR}/test_rms_norm.py
    WORKING_DIRECTORY ${CMAKE_BINARY_DIR})
  set_tests_properties(test_rms_norm_bs_symbolic PROPERTIES LABELS
                                                            "RUN_TYPE=CINN")
  # add_test(
  #   NAME test_rms_norm_reduce_symbolic
  #   COMMAND
  #     ${CMAKE_COMMAND} -E env
  #     PYTHONPATH=${CMAKE_BINARY_DIR}:${CMAKE_BINARY_DIR}/python/:$ENV{PYTHONPATH}
  #     FLAGS_cinn_convert_static_dim_to_dynamic_dim=768:S0
  #     FLAGS_cinn_bucket_compile=True FLAGS_enable_pir_api=1 ${PYTHON_EXECUTABLE}
  #     ${CMAKE_CURRENT_SOURCE_DIR}/test_rms_norm.py
  #   WORKING_DIRECTORY ${CMAKE_BINARY_DIR})
  # set_tests_properties(test_rms_norm_reduce_symbolic PROPERTIES LABELS
  #                                                               "RUN_TYPE=CINN")
  add_test(
    NAME test_rms_norm_symbolic
    COMMAND
      ${CMAKE_COMMAND} -E env
      PYTHONPATH=${CMAKE_BINARY_DIR}:${CMAKE_BINARY_DIR}/python/:$ENV{PYTHONPATH}
      FLAGS_cinn_convert_static_dim_to_dynamic_dim=2048:S0,7:S1
      FLAGS_group_schedule_tiling_first=1 FLAGS_cinn_bucket_compile=True
      FLAGS_enable_pir_api=1 ${PYTHON_EXECUTABLE}
      ${CMAKE_CURRENT_SOURCE_DIR}/test_rms_norm.py
    WORKING_DIRECTORY ${CMAKE_BINARY_DIR})
  set_tests_properties(test_rms_norm_symbolic PROPERTIES LABELS "RUN_TYPE=CINN")
  add_test(
    NAME test_rope_seq_len_symbolic
    COMMAND
      ${CMAKE_COMMAND} -E env
      PYTHONPATH=${CMAKE_BINARY_DIR}:${CMAKE_BINARY_DIR}/python/:$ENV{PYTHONPATH}
      FLAGS_cinn_convert_static_dim_to_dynamic_dim=2048:S1
      FLAGS_group_schedule_tiling_first=1 FLAGS_cinn_bucket_compile=True
      FLAGS_enable_pir_api=1 FLAGS_prim_all=True ${PYTHON_EXECUTABLE}
      ${CMAKE_CURRENT_SOURCE_DIR}/test_rope.py
    WORKING_DIRECTORY ${CMAKE_BINARY_DIR})
  set_tests_properties(test_rope_seq_len_symbolic PROPERTIES LABELS
                                                             "RUN_TYPE=CINN")

  # add_test(
  #   NAME test_rope_bs_symbolic
  #   COMMAND
  #     ${CMAKE_COMMAND} -E env
  #     PYTHONPATH=${CMAKE_BINARY_DIR}:${CMAKE_BINARY_DIR}/python/:$ENV{PYTHONPATH}
  #     FLAGS_cinn_convert_static_dim_to_dynamic_dim=61:S0
  #     FLAGS_cinn_bucket_compile=True FLAGS_enable_pir_api=1 FLAGS_prim_all=True
  #     FLAGS_group_schedule_tiling_first=1 ${PYTHON_EXECUTABLE}
  #     ${CMAKE_CURRENT_SOURCE_DIR}/test_rope.py
  #   WORKING_DIRECTORY ${CMAKE_BINARY_DIR})
  # set_tests_properties(test_rope_bs_symbolic PROPERTIES LABELS "RUN_TYPE=CINN")

  add_test(
    NAME test_rope_symbolic
    COMMAND
      ${CMAKE_COMMAND} -E env
      PYTHONPATH=${CMAKE_BINARY_DIR}:${CMAKE_BINARY_DIR}/python/:$ENV{PYTHONPATH}
      FLAGS_cinn_convert_static_dim_to_dynamic_dim=61:S0,2048:S1
      FLAGS_group_schedule_tiling_first=1 FLAGS_cinn_bucket_compile=True
      FLAGS_enable_pir_api=1 FLAGS_prim_all=True ${PYTHON_EXECUTABLE}
      ${CMAKE_CURRENT_SOURCE_DIR}/test_rope.py
    WORKING_DIRECTORY ${CMAKE_BINARY_DIR})
  set_tests_properties(test_rope_symbolic PROPERTIES LABELS "RUN_TYPE=CINN")

endif()
