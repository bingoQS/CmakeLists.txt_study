## CmakeLists.txt sudy ##

## 一、常用命令 ##

### 1. 指定cmake的最小版本

```cmake
cmake_minimum_required(VERSION 3.4.1)
```

这句话可以不写，但再有的情况下，如果CMakeLists.txt中使用了一些更高版本cmake特有的一些命令时，就需要加上这一行，题型用户应升级到这个版本。

### 2. **设置名称**

```cmake
project(demo)
```

这个命令最好加上，但不是强制的。它会引入亮哥哥变量demo_BINART_DIR和demo_SOURCE_DIR，同时，cmake自动定义了两个等价的变量PROJECCT_BINART_DIR和PROJECT_SOURCE_DIR。

### 3. **设置编译类型**

```cmake
add_executable(demo demo.cpp)
add_library(common STATIC util.cpp)# 生成静态库
add_library(common SHARED util.cpp)# 生成动态库
```

- 在Linux下是：

   demo

​		libcommon.a

​		libcommon.so

- 在Windows下是：

  demo.exe

  common.lib

  common.dll

### 4. **指定编译包的源文件**

#### 4.1 明确指定包含哪些源文件

```cmake
add_library(demo demo.cpp test.cpp util.cpp)
```

#### 4.2 搜索所有的 cpp 文件

aux_source_directory( dir VAR)发现一个目录下所有的源码文件并将列表存在一个变量中。

```cmake
aux_source_directory( . SRC_LIST)# 搜索当前目录下的所有的CPP文件
add_library(demo ${SRC_LIST})
```

#### 4.3 自定义搜索规则

```cmake
file(GLOB SRC_LIST "*.cpp" "protocol/*.cpp")
add_library(demo ${SRC_LIST})
# 或者
file(GLOB SRC_LIST "*.cpp")
file(GLOB SRC_PROTOCOL_LIST "protocol/*.cpp")
add_library(demo ${SRC_LIST} ${SRC_PROTOCOL_LIST})
# 或者
aux_source_directory(. SRC_LIST)
aux_source_directory(protocol SRC_PROTOCOL_LIST)
add_library(demo ${SRC_LIST} ${SRC_PROTOCOL_LIST})
```

### 5. 查找指定的库文件

find_library( VAR name path)查找到指定的预编译库，并将它的路径存在变量中。

默认的搜索路径为cmake的系统库，因此如果是NDK的公共库只需指定库的名字即可。

```cmake
find_library(
	# set the name of the path variable
	log-lib
	
	# you want CMake to locate.
    log
)
# 类似的命令还有 find_file()、find_path()、fin_program()、find_package()
```

### 6. 设置包含的目录

```cmake
include_directories(
	${CMAKE_CURRENT_SOURCE_DIR}
    ${CMAKE_CURRENT_BINARY_DIR}
    ${CMAKE_CURRENT_SOURCE_DIR}/include
)
```

Linux下还可以通过如下的方式设置包含的目录

```cmake
set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -I${CMAKE_CURRENT_SOURCE_DIR}")
```

### 7.  设置编译时的链接库的搜索目录

```cmake
link_directories(
	${CMAKE_SURRENT_SOURCE_DIR}/libs
)
```

Linux下还可以通过如下的方式设置

```cmake
set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -L${CMAKE_CURRENT_SOURCE_DIR}/libs")
```

### 8. 设置target需要链接的库

```cmake
target_link_libraries(
	# 目标库
	demo
	
	# 目标需要链接的库
	${log-lib}
)
```

#### 8.1 指定链接动态库或者静态库

```cmake
target_link_libraries(demo libface.a)
target_link_libraries(demo libface.so)
```

#### 8.2 指定全路径

```cmake
target_link_libraries(demo ${CMAKE_CURRENT_SOURCE_DIR}/libs/libface.a)
target_link_libraries(demo ${CMAKE_CURRENT_SOURCE_DIR}/libs/libface.so)
```

#### 8.3 指定链接多个库

```cmake
target_link_libraries(demo
    ${CMAKE_CURRENT_SOURCE_DIR}/libs/libface.a
    boost_system.a
    boost_thread
    pthread)
```

### 9. 设置变量

#### 9.1 set 直接设置变量的值

```cmake
set(SRC_LIST main.cpp test.cpp)
add_executable(demo ${SRC_LIST})
```

#### 9.2 set 追加设置变量的值

```cmake
set(SRC_LIST main.cpp)
set(SRC_LIST ${SRC_LIST} test.cpp)
add_executable(demo ${SRC_LIST})
```

#### 9.3 list 追加或者删除变量的值

```cmake
set(SRC_LIST main.cpp)
list(APPEND SRC_LIST test.cpp)
list(REMOVE_ITEM SRC_LIST main.cpp)
add_executable(demo ${SRC_LIST})
```



