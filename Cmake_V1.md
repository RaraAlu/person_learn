

### 基本工作项目目录搭建

#### 1. 在根文件目录下创建CMakeLists.txt

```cmake
cmake_minimum_required(VERSION 3.22)

project(SOLDIERFIRE)

set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -g -O2 -Wall")
#创建的buil文件夹的上层文件夹创建bin文件夹，也就是根目录下创建bin文件夹用来存放可执行文件
set(CMAKE_RUNTIME_OUTPUT_DIRECTORY ../bin)
#头文件所在地
include_directories(include)
#库文件所在地
add_subdirectory(src)
#对生成的可执行文件命名
set(EXECUTABLE_NAME my_cmake_exe)
#选中工作空间中需要调用的测试文件
set(WORKING_DIRECTORY
    work/main.cpp
)
#生成可执行文件
add_executable(${EXECUTABLE_NAME} ${WORKING_DIRECTORY})
#将生成的可执行文件对库文件进行链接
target_link_libraries(${EXECUTABLE_NAME} 生成的库文件名)
```

#### 2.在 src  目录下创建 CMakeLists.txt

```cmake
# 添加源文件到变量
file(GLOB SOURCES ${CMAKE_CURRENT_SOURCE_DIR}/*.cpp)
# 生成库文件
add_library(<生成的库文件名> ${SOURCES})
```

