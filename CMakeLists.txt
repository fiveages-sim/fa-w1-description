cmake_minimum_required(VERSION 3.14)
project(fiveages_w1_description)

find_package(ament_cmake REQUIRED)

install(
		DIRECTORY meshes urdf xacro config launch
		DESTINATION share/${PROJECT_NAME}/
)

ament_package()