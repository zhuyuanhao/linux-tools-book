通过 CMake 管理的项目主目录下会有一个 CMakeLists.txt ，其中会包括工程包含哪些子目录等内容。 而在每个子目录下，也会包含一个 CMakeLists.txt， 用来管理该子目录中相关内容的构建。

一个简单的CMakeLists.txt
```cmake
cmake_minimum_required(VERSION 3.1)  # CMake 版本要求
PROJECT(hello)                       # 项目名称

aux_source_directory(. PROGRAM_SOURCE)  # 将当前目录所有文件添加到变量 PROGRAM_SOURCE 中

add_executable(hello ${PROGRAM_SOURCE}) # 指定目标可执行文件 hello
```

编译的时候，一般建立单独的文件夹，让编译过程文件和源代码区分出来，以下是一种编译的方式, 当前目录为项目源代码目录：
```shell
$ mkdir build
$ cd build
$ cmake ..
$ make
```
这样操作之后，编译的目标会位于 build 中，不会和源代码混在一起