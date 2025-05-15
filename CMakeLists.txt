# Set the minimum CMake version required for this project
cmake_minimum_required(VERSION 3.10)
# Set the name of the project, if you want to modify your project name just change MY_PROJECT_NAME by whatever you want
set(PROJECT_NAME "Proyecto")

if(WIN32)
    # set the gcc and g++ compilers path
    set(CMAKE_C_COMPILER "C:\\msys64\\ucrt64\\bin\\gcc.exe" CACHE STRING "C Compiler" FORCE)
    set(CMAKE_CXX_COMPILER "C:\\msys64\\ucrt64\\bin\\g++.exe" CACHE STRING "C++ Compiler" FORCE)
endif()


# naming the project
project(${PROJECT_NAME})

file(COPY "${CMAKE_SOURCE_DIR}/Shaders" DESTINATION "${CMAKE_BINARY_DIR}")
file(COPY "${CMAKE_SOURCE_DIR}/Images" DESTINATION "${CMAKE_BINARY_DIR}")

# Clear the former CMakeCache.txt
if(EXISTS "${CMAKE_BINARY_DIR}/CMakeCache.txt")
    file(REMOVE "${CMAKE_BINARY_DIR}/CMakeCache.txt")
endif()

set(CMAKE_CXX_STANDARD 20)

# Add executable and glad.c
add_executable(${PROJECT_NAME} main.cpp Libs/glad/glad.c
        Libs/stb/stb_image.cpp
        Libs/stb/stb_image.h
        Libs/VAO.cpp
        Libs/VAO.h
        Libs/VBO.cpp
        Libs/VBO.h
        Libs/EBO.cpp
        Libs/EBO.h
        Libs/shaderClass.cpp
        Libs/shaderClass.h
        Libs/Texture.cpp
        Libs/Texture.h
        Libs/Camara.h
        Libs/Camera.cpp

)

# Properly include the Glad header files for this target
target_include_directories(${PROJECT_NAME} PRIVATE ${CMAKE_SOURCE_DIR}/Libs)

# Find OpenGL
set(OpenGL_GL_PREFERENCE GLVND)
find_package(OpenGL REQUIRED)

# Detect OS and set appropriate settings
if (WIN32)
    message(STATUS "Compiling on Windows")

    # Set GLFW static library for Windows (Precompiled for MinGW)
    set(GLFW_LIBRARY_PATH "${CMAKE_SOURCE_DIR}/Libs/GLFW/libglfw3.a")
    set(GLUT_LIBRARY_PATH "${CMAKE_SOURCE_DIR}/Libs/GL/libfreeglut.a")
    set(GLEW_LIBRARY_PATH "${CMAKE_SOURCE_DIR}/Libs/GL/libglew32.a")
    set(GLM_LIBRARY_PATH "${CMAKE_SOURCE_DIR}/Libs/glm/libglm.a")

    # Link against OpenGL and necessary Windows libraries
    target_link_libraries(${PROJECT_NAME} PRIVATE ${GLFW_LIBRARY_PATH} ${GLUT_LIBRARY_PATH} ${GLEW_LIBRARY_PATH} ${GLM_LIBRARY_PATH} OpenGL::GL glu32 gdi32 winmm)

elseif (UNIX)
    message(STATUS "Compiling on Linux")

    # Find GLFW and GLUT (installed on system)
    # install them by using your package manager, Arch:sudo pacman -S glfw glu freeglut Ubuntu:sudo apt install libglfw3-dev libgl1-mesa-dev freeglut3 freeglut3-dev glew-utils binutils-gold
    find_package(glfw3 REQUIRED)
    find_package(GLUT REQUIRED)
    find_package(GLEW REQUIRED)
    set(GLM_LIBRARY_PATH "${CMAKE_SOURCE_DIR}/Libs/glm/x64-linux/libglm.a")
    # Link against OpenGL, GLFW, and GLUT
    target_link_libraries(${PROJECT_NAME} PRIVATE ${GLM_LIBRARY_PATH} OpenGL::GL glfw GLU glut GLEW)
endif()

