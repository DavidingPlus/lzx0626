message(STATUS "configuring snippet...")

# 获取 snippet 目录下所有子目录
file(GLOB TEST_SUBDIRS "${CMAKE_CURRENT_SOURCE_DIR}/*")

# 遍历每个子目录
foreach(test_dir ${TEST_SUBDIRS})

    # 获取测试目录的名称
    get_filename_component(test_name ${test_dir} NAME)

    # 检查是否存在 .buildme 文件
    if(EXISTS "${test_dir}/.buildme")
        # 添加当前测试目录下的所有源文件
        file(GLOB test_sources "${test_dir}/*.cpp")

        # 生成对应的可执行文件
        add_executable(${test_name} ${test_sources})

        # 设置生成的可执行文件输出路径为 build/snippet 目录下
        set_target_properties(${test_name} PROPERTIES
                              RUNTIME_OUTPUT_DIRECTORY ${CMAKE_BINARY_DIR}/snippet/${test_name})

        # 遍历每个库，保证库和头文件都能正常包含
        foreach(subdir ${SUBDIRS})

            # 获取子目录的名称（假设子目录名即为库名）
            get_filename_component(libname ${subdir} NAME)

            # 链接静态库
            target_link_libraries(${test_name} lzx0626${libname})

            # 包含头文件路径
            target_include_directories(${test_name} PRIVATE ${CMAKE_SOURCE_DIR}/src/${libname})

        endforeach()
    else()
        message(STATUS "Skipping ${test_name} as .buildme file is not present.")
    endif()

endforeach()