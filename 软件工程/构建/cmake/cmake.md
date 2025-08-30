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

## target_include_directories
```
target_include_directories(TARGET target_name
                          [BEFORE | AFTER]
                          [SYSTEM] [PUBLIC | PRIVATE | INTERFACE]
                          [items1...])
```
            
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
## set_target_properties
用于为构建目标（如可执行文件、静态库或共享库）设置各种属性。
### 语法
```cmake
set_target_properties(<target1> <target2> ...
    PROPERTIES
    <property1> <value1>
    <property2> <value2>
    ...
)
```
+ <target>：通过 add_executable() 或 add_library() 创建的目标名称；
+ PROPERTIES：固定关键字，后接属性键值对；
### 举例
```cmake
add_library(my_shared SHARED src.cpp)
set_target_properties(my_shared PROPERTIES
    OUTPUT_NAME "engine"
    PREFIX ""
    SUFFIX ".so.1"
    LIBRARY_OUTPUT_DIRECTORY "${PROJECT_BINARY_DIR}/lib"
)
```
## add_custom_target
用于创建不生成文件的自定义目标（例如：执行脚本、运行测试、生成文档等）.
### 语法
```cmake
add_custom_target(Name [ALL]
    [COMMAND command1 [args1...]]
    [COMMAND command2 [args2...] ...]
    [DEPENDS depend1 depend2 ...]
    [WORKING_DIRECTORY dir]
    [COMMENT "text"]
    [VERBATIM]
    [SOURCES src1 [src2...]]
    [BYPRODUCTS files...]
)
```
+ Name	自定义目标的名称（可通过 make Name 或 ninja Name 执行）
+ ALL	将该目标添加到默认构建目标（即运行 make/ninja 时自动执行）
+ COMMAND	要执行的命令（可多组，按顺序执行）
+ DEPENDS	依赖的其他目标或文件（触发重建条件）
+ WORKING_DIRECTORY	命令执行的工作目录
+ COMMENT	构建时显示的提示信息
+ VERBATIM	禁止转义参数（推荐始终启用）
+ SOURCES	关联的源文件（IDE 中显示用）
+ BYPRODUCTS	声明命令生成的副产品（供 Ninja 等构建系统跟踪）

### 举例
```cmake
add_custom_target(run_script
    COMMAND ${PYTHON_EXECUTABLE} ${CMAKE_SOURCE_DIR}/scripts/build_docs.py
    WORKING_DIRECTORY ${CMAKE_BINARY_DIR}
    COMMENT "Generating API documentation..."
)
```
## add_custom_command
用于定义生成特定文件的定制规则。与 add_custom_target 不同，它直接关联到文件生成过程，会在依赖项变更时自动触发。
### 语法
```cmake
add_custom_command(
  OUTPUT output1 [output2...]
  COMMAND command1 [args1...]
  [COMMAND command2 [args2...] ...]
  [DEPENDS depend1 depend2...]
  [WORKING_DIRECTORY dir]
  [COMMENT "text"]
  [VERBATIM]
  [BYPRODUCTS files...]
)
```
+ OUTPUT	指定生成的文件（必须绝对路径）
+ COMMAND	生成文件的命令（可多组顺序执行）
+ DEPENDS	依赖的文件/目标（触发重建条件）
+ TARGET	关联的目标（用于构建挂钩）
+ PRE_BUILD	在目标编译前执行（MSVC特有）
+ PRE_LINK	在编译后链接前执行
+ POST_BUILD	在目标构建完成后执行
+ BYPRODUCTS	Ninja构建系统需要的副产物声明
### 举例
```cmake
# 生成Proto的C++文件
add_custom_command(
  OUTPUT ${PROTO_GEN_SRCS}
  COMMAND protoc --cpp_out=${GEN_DIR} ${PROTO_FILE}
  DEPENDS ${PROTO_FILE}
  COMMENT "Generating C++ protobuf code"
)

# 将生成的源码加入库
add_library(proto_lib ${PROTO_GEN_SRCS})
```
构建后处理（二进制打包）
```cmake
add_executable(my_app main.cpp)

add_custom_command(
  TARGET my_app POST_BUILD
  COMMAND ${CMAKE_COMMAND} -E tar cfvz ${CMAKE_BINARY_DIR}/package.tgz $<TARGET_FILE:my_app>
  COMMENT "Creating deployment package"
)
```
自定义预处理
```cmake
add_custom_command(
  OUTPUT ${CMAKE_CURRENT_BINARY_DIR}/processed.cpp
  COMMAND ${PYTHON_EXECUTABLE} preprocess.py ${SRC} > ${CMAKE_CURRENT_BINARY_DIR}/processed.cpp
  DEPENDS ${SRC} preprocess.py
  VERBATIM
)

add_executable(app ${CMAKE_CURRENT_BINARY_DIR}/processed.cpp)
```

## list
多功能命令，用于操作和管理列表变量。
### 语法
```cmake
list(SUBCOMMAND <list> [args...])
```
+ <list> 是变量名（无需 ${} 包裹）
+ SUBCOMMAND 指定操作类型（如 APPEND、INSERT 等）
  + APPEND	追加元素	list(APPEND MY_LIST "new_item")
  + INSERT	插入元素	list(INSERT MY_LIST 0 "first")
  + REMOVE_ITEM	删除匹配项	list(REMOVE_ITEM MY_LIST "value")
  + REMOVE_AT	按索引删除	list(REMOVE_AT MY_LIST 0)
  + REMOVE_DUPLICATES	去重	list(REMOVE_DUPLICATES MY_LIST)
  + FILTER	条件过滤	list(FILTER MY_LIST INCLUDE REGEX ".*\.cpp")
  + LENGTH	获取长度	list(LENGTH MY_LIST len)
  + GET	获取元素	list(GET MY_LIST 0 first_item)
  + FIND	查找索引	list(FIND MY_LIST "value" index)
  + JOIN	连接为字符串	list(JOIN MY_LIST ";" joined_str)
  + SORT	排序（字母序）
  + REVERSE	反转顺序

### 用例
```cmake
set(SRC_LIST main.cpp util.hpp test.c)
list(FILTER SRC_LIST INCLUDE REGEX ".*\.cpp$") 
```
## include
用于加载并执行外部 CMake 脚本文件
### 语法
```cmake
include(<file|module> [OPTIONAL] [RESULT_VARIABLE <var>] [NO_POLICY_SCOPE])
```
+ <file|module>：可以是：
  + 完整文件路径（.cmake 文件）
  + 模块名（查找 CMAKE_MODULE_PATH 中的 Find<module>.cmake）
+ OPTIONAL：文件不存在时不报错
+ RESULT_VARIABLE：存储加载结果（成功时为路径，失败时为 NOTFOUND）
+ NO_POLICY_SCOPE：不继承调用者的策略设置

### 举例
(1)加载脚本文件
```cmake
# 加载同级目录的配置
include(${CMAKE_CURRENT_SOURCE_DIR}/config.cmake)

# 动态判断加载
set(MY_FEATURE_ENABLED ON)
if(MY_FEATURE_ENABLED)
    include(feature_setup.cmake)
endif()
```

(2) 使用 CMake 模块
```cmake
# 会从 CMAKE_MODULE_PATH 查找 FindOpenSSL.cmake
include(FindOpenSSL)

# 现代写法（CMake 3.0+）
find_package(OpenSSL REQUIRED)  # 内部仍可能使用 include()
```

### 拓展
（1）错误处理
```cmake
include(optional_config.cmake OPTIONAL RESULT_VARIABLE loaded)
if(NOT loaded)
    message(STATUS "No optional config found")
endif()
```
（2）防止错误包含
```cmake
if(NOT DEFINED MY_MODULE_LOADED)
    include(my_module.cmake)
    set(MY_MODULE_LOADED YES CACHE INTERNAL "Module loaded")
endif()
```
### 对比
+ include()	          当前作用域	   加载脚本/模块	否
+ find_package()	    全局	       查找外部依赖	是（<Package>_FOUND）
+ add_subdirectory()	新建作用域	   引入子项目	否

## option

## if

```cmake
if(condition)
    # 条件成立时执行
elseif(condition2)
    # 分支条件
else()
    # 默认逻辑
endif()
```

