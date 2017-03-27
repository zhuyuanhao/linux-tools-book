CMake使用一种平台无关的 CMakeList.txt 文件来定制编译流程，然后再根据目标平台生成所需的本地化 Makefile 和工程文件，如 Unix 的 Makefile 或 Windows 的 Visual Studio 工程。最后用户使用目标平台上的make命令编译项目。

CMake的主要优势

1. 开放源代码，使用类 BSD 许可发布
2. 跨平台，并可生成 native 编译配置文件
3. 能够管理大型项目
4. 简化编译构建过程和编译过程。Cmake 的工具链非常简单：cmake+make
5. 高效虑，按照 KDE 官方说法，CMake 构建 KDE4 的 kdelibs 要比使用 autotools 来构建 KDE3.5.6 的 kdelibs 快 40%，主要是因为 Cmake 在工具链中没有 libtool
6. 可扩展，可以为 cmake 编写特定功能的模块，扩充 cmake 功能


## 参考资料
* 主页：https://cmake.org/
* 官方Tutorial：https://cmake.org/cmake-tutorial/