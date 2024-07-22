cmake_minimum_required(VERSION 3.26)

if(DEFINED ENV{VCPKG_DEFAULT_TRIPLET} AND NOT DEFINED VCPKG_TARGET_TRIPLET)
    set(VCPKG_TARGET_TRIPLET "$ENV{VCPKG_DEFAULT_TRIPLET}" CACHE STRING "")
endif()

project(Nebula)

set(CMAKE_CXX_STANDARD 20)

# VCPKG
include("D:/Dev/Toolchain/vcpkg/scripts/buildsystems/vcpkg.cmake")

set(cpps
        # ImGui
        ext/imgui/imgui.cpp
        ext/imgui/imgui_draw.cpp
        ext/imgui/imgui_widgets.cpp
        ext/imgui/imgui_tables.cpp

        ext/imgui/imgui_demo.cpp

        # ImGui Implementations
        ext/imgui/backends/imgui_impl_glfw.cpp
        ext/imgui/backends/imgui_impl_opengl3.cpp

        # GLAD
        ext/glad/src/glad.c

        # Source
        # # Entrypoint
        src/main.cpp
        # # Application
        src/Application/Application.cpp
        src/Application/Windows.cpp
        src/Application/Layer.cpp
        # # ImGuiLayer
        src/ImGuiLayer/ImGuiLayer.cpp
        src/ImGuiLayer/ImGuiUtils.cpp
        # # Input
        src/Input/Input.cpp
        src/Input/KeyListener.cpp
        src/Input/MouseListener.cpp
        # # Renderer
        src/Renderer/Renderer.cpp
        src/Renderer/Framebuffer.cpp
        src/Renderer/Shader.cpp
        src/Renderer/Texture.cpp
        # # Scenes
        src/Scenes/Scene.cpp
        src/Scenes/Entity.cpp
        # # Utils
        src/Utils/Logger.cpp
        src/Utils/FileSystem.cpp
)

# Debug No Win32, Release Win32
if (WIN32 AND ${CMAKE_BUILD_TYPE} MATCHES "Debug")
    add_executable(Nebula
            ${cpps}
    )
elseif (WIN32 AND ${CMAKE_BUILD_TYPE} MATCHES "Release")
    add_executable(Nebula WIN32
            ${cpps}
    )
endif ()
# Set Include Directories
target_include_directories(Nebula PUBLIC
        inc
        # CmdLine Parser
        ext/cmdline
        # ImGui
        ext/imgui
        ext/imgui/backends
        # Emscripten
        ext/imgui/examples/libs/emscripten
        # STB
        ext/stb
        # GLAD
        ext/glad/include
        # GLFW
        ext/glfw/include
)

# Set Build -W
target_compile_options(Nebula PRIVATE
        -Wall -Wextra -Weffc++
        -Werror=uninitialized
        -Werror=return-type
        -Wconversion -Wsign-compare
        -Werror=unused-result
        -Werror=suggest-override
        -Wzero-as-null-pointer-constant
        -Wmissing-declarations
        -Wold-style-cast
        -Wnon-virtual-dtor
#        -Werror
)

# OpenGL
find_package(OpenGL REQUIRED)

# Misc Links
target_link_libraries(Nebula PRIVATE
#        pthread
        OpenGL::GL
)

# GLFW
add_subdirectory(ext/glfw)
target_link_libraries(Nebula PRIVATE glfw)

execute_process( COMMAND ${CMAKE_COMMAND} -E copy_directory ${CMAKE_SOURCE_DIR}/res ${CMAKE_BINARY_DIR}/res/)