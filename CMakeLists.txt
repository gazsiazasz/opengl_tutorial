cmake_minimum_required(VERSION 3.16)
project(opengl_tutorial)
set(CMAKE_CXX_STANDARD 14)

set(EXT_DIR ${CMAKE_BINARY_DIR}/ext)
set(CMAKE_PREFIX_PATH ${EXT_DIR})

find_package(GLM CONFIG)
find_package(GLEW CONFIG)
find_package(GLFW3 CONFIG)

if(GLFW3_FOUND AND GLEW_FOUND AND GLM_FOUND)

    add_library(shader common/shader.cpp)
    target_include_directories(shader INTERFACE common)
    target_link_libraries(shader GLEW::glew)

    add_executable(tut-01 tut-01/main.cpp)
    target_link_libraries(tut-01 glfw GLEW::glew glm::glm)

    add_executable(tut-02 tut-02/main.cpp)
    target_link_libraries(tut-02 shader glfw GLEW::glew)
    add_custom_command(TARGET tut-02 PRE_LINK COMMAND cp ${CMAKE_CURRENT_SOURCE_DIR}/tut-02/*.glsl .)

    add_executable(tut-03 tut-03/main.cpp)
    target_link_libraries(tut-03 shader glfw GLEW::glew glm::glm)
    add_custom_command(TARGET tut-03 PRE_LINK COMMAND cp ${CMAKE_CURRENT_SOURCE_DIR}/tut-03/*.glsl .)

    add_executable(tut-04 tut-04/main.cpp)
    target_link_libraries(tut-04 shader glfw GLEW::glew glm::glm)
    add_custom_command(TARGET tut-04 PRE_LINK COMMAND cp ${CMAKE_CURRENT_SOURCE_DIR}/tut-04/*.glsl .)

else()
    include(ExternalProject)
    ExternalProject_add(glfw_ext
            URL https://github.com/glfw/glfw/releases/download/3.3.2/glfw-3.3.2.zip
            CMAKE_ARGS -DGLFW_BUILD_EXAMPLES=OFF -DGLFW_BUILD_TESTS=OFF -DGLFW_BUILD_DOCS=OFF
            -DCMAKE_INSTALL_PREFIX=${EXT_DIR})

    ExternalProject_add(glew_ext
            URL https://sourceforge.net/projects/glew/files/glew/2.1.0/glew-2.1.0.zip/download
            SOURCE_SUBDIR build/cmake
            CMAKE_ARGS -DBUILD_UTILS=OFF -DCMAKE_INSTALL_PREFIX=${EXT_DIR})

    ExternalProject_add(glm_ext
            #URL https://github.com/g-truc/glm/archive/0.9.9.8.zip
            GIT_REPOSITORY https://github.com/g-truc/glm.git
            GIT_TAG 0.9.9.8
            GIT_SHALLOW TRUE
            GIT_PROGRESS TRUE
            PATCH_COMMAND git apply ${CMAKE_SOURCE_DIR}/patch/glm.diff
            CMAKE_ARGS -DGLM_TEST_ENABLE=OFF -DCMAKE_INSTALL_PREFIX=${EXT_DIR})
endif()
