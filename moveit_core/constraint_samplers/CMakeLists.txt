set(MOVEIT_LIB_NAME moveit_constraint_samplers)

add_library(${MOVEIT_LIB_NAME} SHARED
  src/constraint_sampler.cpp
  src/constraint_sampler_manager.cpp
  src/constraint_sampler_tools.cpp
  src/default_constraint_samplers.cpp
  src/union_constraint_sampler.cpp
)
set_target_properties(${MOVEIT_LIB_NAME} PROPERTIES VERSION "${${PROJECT_NAME}_VERSION}")
ament_target_dependencies(${MOVEIT_LIB_NAME}
  urdf
  urdfdom
  urdfdom_headers
  visualization_msgs
)
target_link_libraries(${MOVEIT_LIB_NAME}
  moveit_robot_trajectory
  moveit_robot_state
  moveit_kinematic_constraints
  moveit_kinematics_base
  moveit_planning_scene
)

install(TARGETS ${MOVEIT_LIB_NAME}
        LIBRARY DESTINATION lib
        ARCHIVE DESTINATION lib
        RUNTIME DESTINATION bin
)

install(DIRECTORY include/ DESTINATION include)

if(BUILD_TESTING)
  find_package(orocos_kdl REQUIRED)
  find_package(angles REQUIRED)
  find_package(tf2_kdl REQUIRED)
  find_package(moveit_resources REQUIRED)

  include_directories(${orocos_kdl_INCLUDE_DIRS} ${angles_INCLUDE_DIRS} ${tf2_kdl_INCLUDE_DIRS} ${moveit_resources_INCLUDE_DIRS})

  if(WIN32)
    # set(append_library_dirs "$<TARGET_FILE_DIR:${PROJECT_NAME}>;$<TARGET_FILE_DIR:${PROJECT_NAME}_TestPlugins1>")
  else()
    set(append_library_dirs "${CMAKE_CURRENT_BINARY_DIR};${CMAKE_CURRENT_BINARY_DIR}/../utils;${CMAKE_CURRENT_BINARY_DIR}/../robot_state;${CMAKE_CURRENT_BINARY_DIR}/../planning_scene;${CMAKE_CURRENT_BINARY_DIR}/../robot_model;${CMAKE_CURRENT_BINARY_DIR}/../kinematics_constraint")
  endif()

  ament_add_gtest(test_constraint_samplers
    test/test_constraint_samplers.cpp
    test/pr2_arm_kinematics_plugin.cpp
    test/pr2_arm_ik.cpp
  )

  target_include_directories(test_constraint_samplers PUBLIC
    ${geometry_msgs_INCLUDE_DIRS}
  )

  target_link_libraries(test_constraint_samplers
    moveit_constraint_samplers
    moveit_kinematic_constraints
    moveit_planning_scene
    moveit_test_utils
    moveit_utils
    moveit_robot_state
    ${MOVEIT_LIB_NAME}
    ${angles_LIBRARIES}
    ${urdf_LIBRARIES}
    ${tf2_LIBRARIES}
    ${tf2_kdl_LIBRARIES}
    ${tf2_ros_LIBRARIES}
    ${kdl_parser_LIBRARIES}
    ${OCTOMAP_LIBRARIES}
    ${orcos_kdl_LIBRARIES}
    ${urdfdom_LIBRARIES}
    ${urdfdom_headers_LIBRARIES}
    ${rclcpp_headers_LIBRARIES}
    ${geometric_shapes_LIBRARIES}
  )
endif()
