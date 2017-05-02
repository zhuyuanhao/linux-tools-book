glog是Google开发的日志库。glog包括类似于C++输出流的日志API和类似于断言的宏。

## 参考资料
Github：https://github.com/google/glog
官方文档：https://github.com/google/glog/blob/master/doc/glog.html

使用glog的例子
```cpp
#include <glog/logging.h>

int main(int argc, char* argv[]) {
  // Initialize Google's logging library.
  google::InitGoogleLogging(argv[0]);

  // ...
  LOG(INFO) << "Found " << num_cookies << " cookies";
}
```