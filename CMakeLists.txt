cmake_minimum_required(VERSION 3.31.1)
project("Dear Imgui")

add_library(ImGui STATIC
    imgui/imgui_demo.cpp
    imgui/imgui_draw.cpp
    imgui/imgui_tables.cpp
    imgui/imgui_widgets.cpp
    imgui/imgui.cpp
)
target_include_directories(ImGui PUBLIC
    ${CMAKE_CURRENT_BINARY_DIR}/include
)

if(TARGET SDL3::SDL3)
    add_library(ImGui_SDL3 STATIC
        imgui/backends/imgui_impl_sdl3.cpp
    )
    target_include_directories(ImGui_SDL3 PUBLIC
        ${CMAKE_CURRENT_BINARY_DIR}/include
    )
    target_link_libraries(ImGui_SDL3 ImGui SDL3::SDL3)
    add_library(ImGui::SDL3 ALIAS ImGui_SDL3)
endif()

if(DEFINED WEBGPU_BACKEND AND TARGET webgpu)
    add_library(ImGui_WebGPU STATIC
        imgui/backends/imgui_impl_wgpu.cpp
    )
    target_include_directories(ImGui_WebGPU PUBLIC
        ${CMAKE_CURRENT_BINARY_DIR}/include
    )
    target_link_libraries(ImGui_WebGPU ImGui webgpu)

    # Use the wgpu backend defined by the WebGPU-Distribution project
    string(TOUPPER ${WEBGPU_BACKEND} IMGUI_WEBGPU_BACKEND_U)
    if(${IMGUI_WEBGPU_BACKEND_U} STREQUAL "WGPU")
        target_compile_definitions(ImGui_WebGPU PRIVATE IMGUI_IMPL_WEBGPU_BACKEND_WGPU)
    elseif(${IMGUI_WEBGPU_BACKEND_U} STREQUAL "DAWN")
        target_compile_definitions(ImGui_WebGPU PRIVATE IMGUI_IMPL_WEBGPU_BACKEND_DAWN)
    endif()

    add_library(ImGui::WebGPU ALIAS ImGui_WebGPU)
endif()

file(COPY
    imgui/imconfig.h
    imgui/imgui_internal.h
    imgui/imgui.h
    imgui/imstb_rectpack.h
    imgui/imstb_textedit.h
    imgui/imstb_truetype.h
    imgui/backends/imgui_impl_sdl3.h
    imgui/backends/imgui_impl_wgpu.h
    DESTINATION ${CMAKE_CURRENT_BINARY_DIR}/include
)
