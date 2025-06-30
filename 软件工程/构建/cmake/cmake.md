# 命令
## cmake_minimum_required
指定最低 CMake 版本
```cmake
cmake_minimum_required (VERSION 2.6)
```
## project(<project_name> [<language>...])
定义项目名称和使用的编程语言
```cmake
project(MyProject)
```
## add_executable(<target> <source_files>...)
指定要生成的可执行文件和其源文件
```cmake
add_excutable(main main.cpp)
```
## add_library(<target> <source_files>...)
创建一个库（静态库或动态库）及其源文件
```cmake
add_library(MyLibrary STATIC library.cpp)
```
## target_link_libraries(<target> <libraries>...)
链接目标文件与其他库
```cmake
target_link_libraries(MyExecutable MyLibrary)
```
## set(<variable> <value>...)
设置变量的值.
```cmake
set(CMAKE_CXX_STANDARD 11)
```

# #target_include_directories(TARGET target_name
                          [BEFORE | AFTER]
                          [SYSTEM] [PUBLIC | PRIVATE | INTERFACE]
                          [items1...])
            
## add_definitions

## message
输出调试信息


## include_directories
添加头文件目录
```cmake
include_directories(include)
```
## aux_source_directory 
添加目录下的源文件并存放在变量当中
```cmake
aux_source_directory(src SRC_LIST)
```
## target_compile_options
用于为特定目标（如可执行文件或库）设置编译器选项的命令。它可以控制编译过程中的优化级别、警告设置、语言标准等;
```cmake
target_compile_options(<target> 
    [BEFORE] 
    <INTERFACE|PUBLIC|PRIVATE> [items1...] 
    [<INTERFACE|PUBLIC|PRIVATE> [items2...] ...])
```
+ <target>：要设置选项的目标（如通过 add_executable() 或 add_library() 创建的目标）;
+ PRIVATE|PUBLIC|INTERFACE：指定选项的可见性:
  + PRIVATE：仅用于当前目标的编译;
  + PUBLIC：用于当前目标及其依赖项；
  + INTERFACE：仅传递给依赖此目标的其他目标；
+ BEFORE：将选项插入到现有选项之前（默认追加到末尾）；
```cmake
add_executable(my_app main.cpp)

# 启用所有警告并视警告为错误（GCC/Clang）
target_compile_options(my_app PRIVATE -Wall -Wextra -Werror)

# MSVC 的等效选项
target_compile_options(my_app PRIVATE /W4 /WX)
```

## target_link_options
用于为可执行文件、共享库或模块库目标添加链接器选项（如 -Wl 标志、库路径等）。
### 核心语法
```cmake
target_link_options(<target> [BEFORE]
  <INTERFACE|PUBLIC|PRIVATE> [items1...]
  [<INTERFACE|PUBLIC|PRIVATE> [items2...] ...])
```
### 举例
```cmake
add_executable(my_app main.cpp)
target_link_options(my_app PRIVATE "-Wl,-rpath,/usr/local/lib")  # Linux
```
