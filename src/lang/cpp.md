# C / C++

## 官方资源

- C 标准: [ISO/IEC 9899](https://www.iso.org/standard/74528.html)
- C++ 官网: [isocpp.org](https://isocpp.org)
- C++ Reference: [cppreference.com](https://en.cppreference.com)
- C++ Standard: [ISO/IEC 14882](https://isocpp.org/std/the-standard)
- Compiler Explorer: [godbolt.org](https://godbolt.org) (在线查看汇编输出)

## 环境安装

```bash
# macOS (Xcode Command Line Tools)
xcode-select --install

# Linux
sudo apt install build-essential gdb    # GCC
sudo apt install clang lldb             # Clang

# Windows
# 安装 Visual Studio (MSVC) 或 MinGW-w64
choco install mingw
```

## 编译器

| 编译器 | 命令 | 说明 |
|--------|------|------|
| GCC | `gcc` / `g++` | GNU 编译器，Linux 默认 |
| Clang | `clang` / `clang++` | LLVM 编译器，macOS 默认 |
| MSVC | `cl` | 微软编译器，Windows |

## 常用命令

```bash
# C
gcc -Wall -Wextra -o output source.c
gcc -std=c17 -O2 -o output source.c

# C++
g++ -Wall -Wextra -o output source.cpp
g++ -std=c++20 -O2 -o output source.cpp

# 调试编译
g++ -g -O0 -fsanitize=address -o output source.cpp

# 运行
./output
```

## 构建工具

```bash
# CMake（最常用）
cmake -B build
cmake --build build
cmake --build build --target <target>

# Make
make
make clean
```

## 推荐学习资源

### C

- [The C Programming Language (K&R)](https://en.wikipedia.org/wiki/The_C_Programming_Language) — 经典中的经典
- [Modern C (Jens Gustedt)](https://gustedt.gitlabpages.inria.fr/modern-c/) — 现代 C 写法
- [Learn C (learn-c.org)](https://www.learn-c.org) — 在线交互教程

### C++

- [C++ Primer (Lippman)](https://www.informit.com/store/c-plus-plus-primer-9780321714114) — 全面入门
- [A Tour of C++ (Stroustrup)](https://www.stroustrup.com/tour2.html) — 快速概览
- [Effective Modern C++ (Scott Meyers)](https://www.oreilly.com/library/view/effective-modern-c/9781491908419/) — 现代 C++ 最佳实践
- [C++ Core Guidelines](https://isocpp.github.io/CppCoreGuidelines/CppCoreGuidelines) — 官方编码规范
- [Learn C++ (learncpp.com)](https://www.learncpp.com) — 免费在线教程

## 常用库

| 类别 | C | C++ |
|------|---|-----|
| 网络 | [libcurl](https://curl.se/libcurl/) | [Boost.Asio](https://www.boost.org/doc/libs/release/doc/html/boost_asio.html) |
| JSON | [cJSON](https://github.com/DaveGamble/cJSON) | [nlohmann/json](https://github.com/nlohmann/json) |
| 日志 | — | [spdlog](https://github.com/gabime/spdlog) |
| 测试 | [Unity](http://www.throwtheswitch.org/unity) | [Google Test](https://github.com/google/googletest)、[Catch2](https://github.com/catchorg/Catch2) |
| 序列化 | — | [Protobuf](https://protobuf.dev) |
| GUI | [GTK](https://www.gtk.org) | [Qt](https://www.qt.io)、[ImGui](https://github.com/ocornut/imgui) |
| 并发 | pthreads | [std::thread](https://en.cppreference.com/w/cpp/thread)、[TBB](https://github.com/uxlfoundation/oneTBB) |
