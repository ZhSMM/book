# CMake基础

[win10 安装Cliion + CMake + MSVC/MinGW 作QT开发](https://ifmet.cn/posts/5783d3ec/)

[披着Clion的外衣实则在讲CMake](https://cloud.tencent.com/developer/article/2244501)



#### 基础

##### 常见的CMake变量

官方文档：[https://cmake.org/cmake/help/latest/manual/cmake-variables.7.html](https://cmake.org/cmake/help/latest/manual/cmake-variables.7.html)

| 变量                           | 含义                                                         |
| ------------------------------ | ------------------------------------------------------------ |
| PROJECT_NAME                   | 项目名称                                                     |
| PROJECT_BINARY_DIR             | 项目的二进制文件目录，即编译后的可执行文件和库文件的输出目录 |
| PROJECT_SOURCE_DIR             | 项目的源文件目录，即包含 CMakeLists.txt 文件的目录           |
| CMAKE_BINARY_DIR               | 当前 CMake 运行的二进制文件目录                              |
| CMAKE_SOURCE_DIR               | 当前 CMake 运行的源文件目录                                  |
| CMAKE_CURRENT_SOURCE_DIR       | 当前处理的 CMakeLists.txt 所在的路径                         |
| CMAKE_CURRENT_BINARY_DIR       | target 编译目录                                              |
| CMAKE_CURRENT_LIST_DIR         | CMakeLists.txt 的完整路径                                    |
| CMAKE_C_STANDARD               | 指定 C 语言的标准版本                                        |
| CMAKE_CXX_STANDARD             | 指定 C++ 语言的标准版本                                      |
| CMAKE_CXX_FLAGS                | 指定编译 C++ 代码时使用的编译选项                            |
| CMAKE_C_FLAGS                  | 指定编译 C 代码时使用的编译选项                              |
| CMAKE_EXE_LINKER_FLAGS         | 指定链接可执行文件时使用的链接选项                           |
| CMAKE_SYSTEM_NAME              | 指定当前操作系统名称（如 Windows、Linux 等）                 |
| CMAKE_SYSTEM_PROCESSOR         | 指定当前处理器的类型（如 x86、x86_64 等）                    |
| CMAKE_CXX_COMPILER_ID          | 指定当前使用的 C++ 编译器                                    |
| CMAKE_C_COMPILER_ID            | 指定当前使用的 C 编译器                                      |
| LIBRARY_OUTPUT_PATH            | 指定生成的库文件位置（动态库、静态库）                       |
| CMAKE_ARCHIVE_OUTPUT_DIRECTORY | 静态库 lib 输出路径                                          |
| CMAKE_LIBRARY_OUTPUT_DIRECTORY | 指定动态库输出目录                                           |
| CMAKE_RUNTIME_OUTPUT_DIRECTORY | 指定可执行文件输出目录                                       |
| EXECUTABLE_OUTPUT_PATH         | 重新定义目标二进制可执行文件的存放位置                       |



##### 添加头文件目录

include_directories：用于增加一个头文件

```shell
include_directories([AFTER|BEFORE] [SYSTEM] dir1 [dir2 ...])
```

目录支持绝对路径和相对路径，相对路径基于内置变量`CMAKE_CURRENT_SOURCE_DIR`，默认情况下，包含目录是从已存在的包含目录列表后追加的。如果想改变默认行为可以通过如下两种方式：

+ 设置`CMAKE_INCLUDE_DIRECTORIES_BEFORE`为`ON`，即插入到包含目录列表前
+ 直接通过参数`AFTER`和`BEFORE`控制是向后插入（Appending）还是向前插入(Prepending)

注意：**相对路径是相对于当前进行的CMakeLists.txt所在目录**，如当前CMakeLists下的include文件夹，可以写成：`include_directories(include)`或者带上符号`include_directories(./include)`

该命令相当于g++选项中-I参数作用，也相当与环境变量中增加路径到CPLUS_INCLUDE_PATH变量的作用。



##### LINK_DIRECTORIES 添加共享库目录

LINK_DIRECTORIES：添加共享库目录

```shell
link_directories(directory1 directory2 ...)
```

相当于g++命令的-L选项的作用，也相当于环境变量中增加LD_LIBRARY_PATH的路径的作用。



##### FIND_LIBRARY 查找库所在目录

FIND_LIBRARY：查找库所在目录

```shell
# 简写
find_library (<VAR> name1 [path1 path2 ...])

# 完整语法
find_library (
          <VAR>
          name | NAMES name1 [name2 ...] [NAMES_PER_DIR]
          [HINTS path1 [path2 ... ENV var]]
          [PATHS path1 [path2 ... ENV var]]
          [PATH_SUFFIXES suffix1 [suffix2 ...]]
          [DOC "cache documentation string"]
          [NO_DEFAULT_PATH]
          [NO_CMAKE_ENVIRONMENT_PATH]
          [NO_CMAKE_PATH]
          [NO_SYSTEM_ENVIRONMENT_PATH]
          [NO_CMAKE_SYSTEM_PATH]
          [CMAKE_FIND_ROOT_PATH_BOTH |
           ONLY_CMAKE_FIND_ROOT_PATH |
           NO_CMAKE_FIND_ROOT_PATH]
         )
```

示例：cmake会在目录中查找，如果所有目录中都没有，值RUNTIME_LIB就会被赋为NO_DEFAULT_PATH

```shell
FIND_LIBRARY(RUNTIME_LIB rt /usr/lib  /usr/local/lib NO_DEFAULT_PATH)
```



##### LINK_LIBRARIES 添加需要链接的库文件

LINK_LIBRARIES：添加需要链接的库文件路径，支持链接一个或多个，使用空格分隔

```shell
link_libraries(library1 <debug | optimized> library2 ...)
```

示例：

```shell
# 支持只输入库名，然后在LINK_DIRECTORIES指定目录搜索
# 也支持全路径
link_libraries(iconv　"/opt/MATLAB/R2012a/bin/glnxa64/libmx.so")
```



##### TARGET_LINK_LIBRARIES 设置需要链接的库文件

TARGET_LINK_LIBRARIES：设置要链接的库文件的名称

```shell
target_link_libraries(<target> [item1 [item2 [...]]]
                      [[debug|optimized|general] <item>] ...)
```

示例：

```shell
target_link_libraries(myProject comm) # 连接libhello.so库，默认优先链接动态库
target_link_libraries(myProject libcomm.a) # 显示指定链接静态库
target_link_libraries(myProject libcomm.so) # 显示指定链接动态库
```



##### add_executable 生成目标文件

```shell
add_executable(<name> [WIN32] [MACOSX_BUNDLE]
               [EXCLUDE_FROM_ALL]
               source1 [source2 ...])
```

示例：

```shell
add_executable(demo main.cpp)
```



#### 场景写法

##### 判断操作系统平台

CMake支持两种判断操作系统平台的方法：

```cmake
# 方式一：利用内置变量CMAKE_SYSTEM_NAME
MESSAGE(STATUS "operation system is ${CMAKE_SYSTEM}")
 
IF (CMAKE_SYSTEM_NAME MATCHES "Linux")
	MESSAGE(STATUS "current platform: Linux ")
ELSEIF (CMAKE_SYSTEM_NAME MATCHES "Windows")
	MESSAGE(STATUS "current platform: Windows")
ELSEIF (CMAKE_SYSTEM_NAME MATCHES "FreeBSD")
	MESSAGE(STATUS "current platform: FreeBSD")
ELSE ()
	MESSAGE(STATUS "other platform: ${CMAKE_SYSTEM_NAME}")
ENDIF (CMAKE_SYSTEM_NAME MATCHES "Linux")

# 方式二：
IF (WIN32)
	MESSAGE(STATUS "Now is windows")
ELSEIF (APPLE)
	MESSAGE(STATUS "Now is Apple systens.")
ELSEIF (UNIX)
	MESSAGE(STATUS "Now is UNIX-like OS's.")
ENDIF ()
```



##### 指定头文件、链接库生成lib文件

```cmake
# 指定头文件
list(APPEND INC_DIR
        ${PROJECT_SOURCE_DIR}/include/mysql
        ${PROJECT_SOURCE_DIR}/thirdParty/mysql/include)
include_directories(${INC_DIR})

# 指定lib库目录
list(APPEND LINK_DIR ${PROJECT_SOURCE_DIR}/thirdParty/mysql/lib)
link_directories(${LINK_DIR})
# 指定链接库
link_libraries(libmysql)

# 指定库文件输出路径
set(LIBRARY_OUTPUT_PATH ${PROJECT_SOURCE_DIR}/lib/mysql)
# 指定资源
set(SRC_LISTS
        Connection.cpp
        MySqlConnectionPool.cpp)

# 指定静态库输出目录
set(CMAKE_ARCHIVE_OUTPUT_DIRECTORY ${PROJECT_SOURCE_DIR}/lib/mysql)
# 生成静态链接库
add_library(mysql-pool STATIC ${SRC_LISTS})
# 指定静态链接库对外暴露的头文件
target_include_directories(mysql-pool PUBLIC ${PROJECT_SOURCE_DIR}/include/mysql)

# 指定动态库输出目录
set(CMAKE_LIBRARY_OUTPUT_DIRECTORY ${PROJECT_SOURCE_DIR}/lib/mysql)
# 生成动态链接库
add_library(mysql-pool-shared SHARED ${SRC_LISTS})
# 指定动态链接库对外暴露的头文件
target_include_directories(mysql-pool-shared PUBLIC ${PROJECT_SOURCE_DIR}/include/mysql)
```



##### 遍历获取所有文件名

```cmake
# 把./src目录下所有符合*.cpp结尾的文件存入USER_LIBS_PATH变量中
file(GLOB USER_LIBS_PATH ./src/*.cpp)

# 不但在当前目录需要引入，还需要在当前目录子目录引入，即递归引入
file(GLOB_RECURSE USER_LIBS_PATH ./src/*.cpp)
```

示例：获取多个目录下的源文件并加入到集合

```cmake
# 获取Classes目录下所有代码文件
set(SRC_CLASSES)
file(GLOB SRC_CLASSES Classes/*.*)

# 将文件加入list
list(APPEND GAME_SOURCE_MINE ${SRC_CLASSES})
```



##### 设置Debug版本和Release版本下库文件的后缀名

```cmake
set(CMAKE_DEBUG_POSTFIX "_d")
set(CMAKE_RELEASE_POSTFIX "_r")
```



##### 设置Debug版本和Release版本下可执行文件的后缀名

```cmake
set_target_properties(${TARGET_NAME} PROPERTIES DEBUG_POSTFIX "_d") 
set_target_properties(${TARGET_NAME} PROPERTIES RELEASE_POSTFIX "_r")
```



##### 设置生成的target配置安装目录 

```cmake
SET(CMAKE_INSTALL_PREFIX <你要安装的路径>)

install(TARGETS MyLib
        EXPORT MyLibTargets 
        LIBRARY DESTINATION lib  # 动态库安装路径
        ARCHIVE DESTINATION lib  # 静态库安装路径
        RUNTIME DESTINATION bin  # 可执行文件安装路径
        PUBLIC_HEADER DESTINATION include  # 头文件安装路径
        )
```

+ LIBRARY，ARCHIVE，RUNTIME，PUBLIC_HEADER是可选的，可以根据需要进行选择。 
+ DESTINATION后面的路径可以自行指定，根目录默认为`CMAKE_INSTALL_PREFIX`，可以用`set`方法进行指定，如果使用默认值的话，类Unix系统的默认值为 `/usr/local`，Windows的默认值为 `c:/Program Files/${PROJECT_NAME}`。比如linux系统下若LIBRARY的安装路径指定为`lib`，即为`/usr/local/lib`

