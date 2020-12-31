cmake_minimum_required(VERSION 3.15)
project(pipbot)

if(MSVC)
  ## Compile as at least C++17 for cppwinrt
  add_compile_options(/Od)
  add_compile_options(/std:c++latest)
  add_compile_options(/await)

  add_definitions(-D_CRT_SECURE_NO_WARNINGS)
  add_definitions(-D_SILENCE_ALL_CXX17_DEPRECATION_WARNINGS)
  endif()

set(FETCHCONTENT_QUIET OFF)

include(FetchContent)
FetchContent_Declare(
  imgui
  GIT_REPOSITORY https://github.com/ocornut/imgui
  GIT_TAG        v1.79
)
FetchContent_MakeAvailable(imgui)

FetchContent_Declare(
  glfw
  GIT_REPOSITORY https://github.com/glfw/glfw
  GIT_TAG        3.3.2
)
FetchContent_MakeAvailable(glfw)

FetchContent_Declare(
    SDL2
    GIT_REPOSITORY https://github.com/spurious/SDL-mirror
)
FetchContent_MakeAvailable(SDL2)


find_package(catkin REQUIRED COMPONENTS
  roscpp
  roslib
  std_msgs
  cv_bridge
)

find_package(OpenGL REQUIRED)

#get_cmake_property(_variableNames VARIABLES)
#foreach (_variableName ${_variableNames})
#  message(STATUS "${_variableName}=${${_variableName}}")
#endforeach()

catkin_package(
#  INCLUDE_DIRS include
  CATKIN_DEPENDS 
    roscpp 
    roslib 
    std_msgs
)

include_directories(
  include
  ${catkin_INCLUDE_DIRS}
  ${OPENGL_INCLUDE_DIRS}
  ${glfw_INCLUDE_DIRS}
  ${imgui_SOURCE_DIR}
  ${imgui_SOURCE_DIR}/examples
  ${imgui_SOURCE_DIR}/examples/libs/gl3w
  ${imgui_SOURCE_DIR}/examples/libs/glfw/include  
)

add_executable(${PROJECT_NAME}_node 
  src/main.cpp
  ${imgui_SOURCE_DIR}/imgui.cpp
  ${imgui_SOURCE_DIR}/imgui_demo.cpp
  ${imgui_SOURCE_DIR}/imgui_draw.cpp
  ${imgui_SOURCE_DIR}/imgui_widgets.cpp
  ${imgui_SOURCE_DIR}/examples/imgui_impl_sdl.cpp
  ${imgui_SOURCE_DIR}/examples/imgui_impl_opengl3.cpp
  ${imgui_SOURCE_DIR}/examples/libs/gl3w/GL/gl3w.c  
)
add_dependencies(${PROJECT_NAME}_node 
  ${${PROJECT_NAME}_EXPORTED_TARGETS} 
  ${catkin_EXPORTED_TARGETS}
)

target_link_libraries(${PROJECT_NAME}_node
  glfw 
  GLEW
  ${catkin_LIBRARIES}
  OpenGL::GL
  ${OPENGL_LIBRARIES}
  ${CMAKE_DL_LIBS}
  SDL2 
  SDL2main
)

install(TARGETS ${PROJECT_NAME}_node
  ARCHIVE DESTINATION ${CATKIN_PACKAGE_LIB_DESTINATION}
  LIBRARY DESTINATION ${CATKIN_PACKAGE_LIB_DESTINATION}
  RUNTIME DESTINATION ${CATKIN_PACKAGE_BIN_DESTINATION}
)

install(DIRECTORY launch
  DESTINATION ${CATKIN_PACKAGE_SHARE_DESTINATION}
)