cmake_minimum_required(VERSION 3.30.0 FATAL_ERROR)

set(CMAKE_BUILD_TYPE Release CACHE STRING "Build type")

if (USE_IMPORT_COKE)
    # https://github.com/Kitware/CMake/blob/v3.30.0/Help/dev/experimental.rst#c-import-std-support
    if (CMAKE_VERSION VERSION_LESS 3.31.8)
        # 3.30.0 ~ 3.31.7
        set(CMAKE_EXPERIMENTAL_CXX_IMPORT_STD "0e5b6991-d74f-4b3d-a41c-cf096e0b2508")
    elseif (CMAKE_VERSION VERSION_LESS 4.0.0)
        # 3.31.8 ~ 3.31.10
        set(CMAKE_EXPERIMENTAL_CXX_IMPORT_STD "d0edc3af-4c50-42ea-a356-e2862fe7a444")
    elseif (CMAKE_VERSION VERSION_LESS 4.0.3)
        # 4.0.0 ~ 4.0.2
        set(CMAKE_EXPERIMENTAL_CXX_IMPORT_STD "a9e1cf81-9932-4810-974b-6eccaf14e457")
    else ()
        # 4.0.3 ~ at least 4.2.1
        set(CMAKE_EXPERIMENTAL_CXX_IMPORT_STD "d0edc3af-4c50-42ea-a356-e2862fe7a444")
    endif ()

    set(CMAKE_CXX_MODULE_STD 1)
endif ()

project(
    import-coke
    VERSION 0.1.0
    LANGUAGES CXX
)

set(CMAKE_CXX_STANDARD 23)
set(CMAKE_CXX_STANDARD_REQUIRED ON)

find_package(OpenSSL REQUIRED)
find_package(Threads REQUIRED)
find_package(workflow REQUIRED)
find_package(coke REQUIRED)

if (USE_IMPORT_COKE)
    add_library(coke_module)

    target_sources(coke_module
        PUBLIC
            FILE_SET cxx_modules
            TYPE CXX_MODULES
            BASE_DIRS ${COKE_INCLUDE_DIR}
            FILES
                ${COKE_INCLUDE_DIR}/coke/module/coke.cppm
                ${COKE_INCLUDE_DIR}/coke/module/coke_tools.cppm
    )

    target_link_libraries(coke_module
        PUBLIC
            coke::coke-static
    )

    set(coke_alias "coke_module")

    add_compile_definitions(USE_IMPORT_COKE=1)
else ()
    set(coke_alias "coke::coke")
endif ()

add_subdirectory(example)
