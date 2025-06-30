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
