CMake 拥有比直接编写 Makefile 更直观的语法，更易于编写和维护。CMake 根据内置的规则和语法来自动生成相关的makefile 文件进行编译，同时还支持静态库和动态库的构建。

CMake的主要优势

1. 开放源代码，使用类 BSD 许可发布。http://cmake.org/HTML/Copyright.html
2. 跨平台，并可生成 native 编译配置文件，在 Linux/Unix 平台，生成 makefile，在苹果平台，可以生成 xcode，在 Windows 平台，可以生成 MSVC 的工程文件。
3. 能够管理大型项目。
4. 简化编译构建过程和编译过程。Cmake 的工具链非常简单：cmake+make。
5. 高效虑，按照 KDE 官方说法，CMake 构建 KDE4 的 kdelibs 要比使用 autotools 来构建 KDE3.5.6 的 kdelibs 快 40%，主要是因为 Cmake 在工具链中没有 libtool。
6. 可扩展，可以为 cmake 编写特定功能的模块，扩充 cmake 功能。


## 参考资料
* 主页：https://cmake.org/
* 官方Tutorial：https://cmake.org/cmake-tutorial/