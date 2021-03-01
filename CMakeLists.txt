cmake_minimum_required(VERSION 3.17)
project(CMake_Exhale)

set(CMAKE_C_STANDARD 11)

# Add the cmake folder so the FindSphinx module is found
set(CMAKE_MODULE_PATH "${PROJECT_SOURCE_DIR}/cmake/modules" ${CMAKE_MODULE_PATH})

add_subdirectory("docs")
