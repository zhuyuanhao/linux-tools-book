可在命令行下向CMake传递自定义变量
`cmake -DMY_VAR=hello .`

`cmake -DCMAKE_BUILD_TYPE=Release .`
`cmake -DCMAKE_BUILD_TYPE=debug .`

`PROJECT_BINARY_DIR`指cmake命令执行时的目录
`PROJECT_SOURCE_DIR`指项目主CMakeLists.txt所在的目录
当执行`cmake .`时（内部编译）它们是相同的，当执行`cmake ..`或其他不在当前目录编译的命令时（外部编译）是不同的。

`EXECUTABLE_OUTPUT_PATH`：可执行二进制的输出路径
`LIBRARY_OUTPUT_PATH`：库的输出路径
在添加`ADD_EXECUTABLE`或`ADD_LIBRARY`命令的CMakeLists.txt文件中指定：
```
SET(EXECUTABLE_OUTPUT_PATH ${PROJECT_BINARY_DIR}/bin)
SET(LIBRARY_OUTPUT_PATH ${PROJECT_BINARY_DIR}/lib)
```


CMAKE_VERBOSE_MAKEFILE on 输出详细的编译和链接信息
CMAKE_CXX_COMPILER "g++" c++编译器
CMAKE_CXX_FLAGS "-Wall" c++编译器参数
CMAKE_CXX_FLAGS_DEBUG 除CMAKE_CXX_FLAGS外，debug版本的额外编译器参数
CMAKE_CXX_FLAGS_RELEASE 除CMAKE_CXX_FLAGS外，release版本的额外编译器参数
EXECUTABLE_OUTPUT_PATH ${PROJECT_SOURCE_DIR}/bin 可执行文件的输出目录
LIBRARY_OUTPUT_PATH ${PROJECT_SOURCE_DIR}/lib 链接库的输出目录


