# 1. 命令行编译 ros2 package

命令行或clion命令和中编译 `dev_ws`：

```bash
colcon build --cmake-args -DCMAKE_EXPORT_COMPILE_COMMANDS=ON -G Ninja# 选项：-DCMAKE_EXPORT_COMPILE_COMMANDS=ON，用于生成 
# In order to be able to open a ROS2 workspace in CLion, we will generate a JSON compilation database using the CMAKE_EXPORT_COMPILE_COMMANDS CMake flag.# 如果没有 Ninja，sudo 安装
sudo apt install ninja-build
```

当前项目目录结构：

```shell
dev_ws
└── src├── cpp_pubsub│   ├── cmake_commands.bat│   ├── CMakeLists.txt│   ├── include│   │   └── cpp_pubsub│   ├── package.xml│   └── src│       ├── publisher_member_function.cpp│       └── subscriber_member_function.cpp└── cpp_srvcli├── cmake_commands.bat├── CMakeLists.txt├── include│   └── cpp_srvcli├── package.xml└── src├── add_two_ints_client.cpp└── add_two_ints_server.cpp10 directories, 10 files
```

结果文件如下：

```bash
jacob@jacob-xx15:~/Work_ROS/ros2_study_ws/dev_ws$ tree . -L 2
.
├── build
│   ├── COLCON_IGNORE
│   ├── compile_commands.json  # -DCMAKE_EXPORT_COMPILE_COMMANDS=ON 生成的代码
│   ├── cpp_pubsub
│   └── cpp_srvcli
├── install
│   ├── ..
│   ├── cpp_pubsub
│   ├── cpp_srvcli
│   ├── ..
├── log
│   ├── build_2023-08-14_19-26-37
│   ├── COLCON_IGNORE
│   ├── latest -> latest_build
│   └── latest_build -> build_2023-08-14_19-26-37
└── src├── cpp_pubsub└── cpp_srvcli13 directories, 14 files
```

生成的 `.json` 文件如下：

```bash
[
{"directory": "/home/jacob/Work_ROS/ros2_study_ws/dev_ws/build/cpp_pubsub","command": "/usr/bin/c++  -DDEFAULT_RMW_IMPLEMENTATION=rmw_fastrtps_cpp -DRCUTILS_ENABLE_FAULT_INJECTION -DSPDLOG_COMPILED_LIB -isystem /opt/ros/foxy/include    -Wall -Wextra -Wpedantic -std=gnu++14 -o CMakeFiles/listener.dir/src/subscriber_member_function.cpp.o -c /home/jacob/Work_ROS/ros2_study_ws/dev_ws/src/cpp_pubsub/src/subscriber_member_function.cpp","file": "/home/jacob/Work_ROS/ros2_study_ws/dev_ws/src/cpp_pubsub/src/subscriber_member_function.cpp"
},{"directory": "/home/jacob/Work_ROS/ros2_study_ws/dev_ws/build/cpp_pubsub","command": "/usr/bin/c++  -DDEFAULT_RMW_IMPLEMENTATION=rmw_fastrtps_cpp -DRCUTILS_ENABLE_FAULT_INJECTION -DSPDLOG_COMPILED_LIB -isystem /opt/ros/foxy/include    -Wall -Wextra -Wpedantic -std=gnu++14 -o CMakeFiles/talker.dir/src/publisher_member_function.cpp.o -c /home/jacob/Work_ROS/ros2_study_ws/dev_ws/src/cpp_pubsub/src/publisher_member_function.cpp","file": "/home/jacob/Work_ROS/ros2_study_ws/dev_ws/src/cpp_pubsub/src/publisher_member_function.cpp"
},{"directory": "/home/jacob/Work_ROS/ros2_study_ws/dev_ws/build/cpp_srvcli","command": "/usr/bin/c++  -DDEFAULT_RMW_IMPLEMENTATION=rmw_fastrtps_cpp -DRCUTILS_ENABLE_FAULT_INJECTION -DSPDLOG_COMPILED_LIB -isystem /opt/ros/foxy/include    -Wall -Wextra -Wpedantic -std=gnu++14 -o CMakeFiles/client.dir/src/add_two_ints_client.cpp.o -c /home/jacob/Work_ROS/ros2_study_ws/dev_ws/src/cpp_srvcli/src/add_two_ints_client.cpp","file": "/home/jacob/Work_ROS/ros2_study_ws/dev_ws/src/cpp_srvcli/src/add_two_ints_client.cpp"
},{"directory": "/home/jacob/Work_ROS/ros2_study_ws/dev_ws/build/cpp_srvcli","command": "/usr/bin/c++  -DDEFAULT_RMW_IMPLEMENTATION=rmw_fastrtps_cpp -DRCUTILS_ENABLE_FAULT_INJECTION -DSPDLOG_COMPILED_LIB -isystem /opt/ros/foxy/include    -Wall -Wextra -Wpedantic -std=gnu++14 -o CMakeFiles/server.dir/src/add_two_ints_server.cpp.o -c /home/jacob/Work_ROS/ros2_study_ws/dev_ws/src/cpp_srvcli/src/add_two_ints_server.cpp","file": "/home/jacob/Work_ROS/ros2_study_ws/dev_ws/src/cpp_srvcli/src/add_two_ints_server.cpp"
}
]
```

------

# 2. 使用 clion 打开 ros2 工程

1. 在 1 中已经生成了 `compile_commands.json`，使用 clion 将其作为项目打开（这个文件有点像 toplevel CMakeLists.txt）。
2. 修改项目 root 目录。

此时 clion 还不能 **build | run | debug** dev_ws 工程的代码。
 ![img](https://img-blog.csdnimg.cn/dad71276fec040cdb0799b564930f16c.png)

------

# 3. 使用 clion 编译整个 ros2 工程

大致步骤：

1. 使用 clion 的 external tool 配置 `colcon build`
2. 编译 dev_ws 工程

## 3.1 使用 clion 的 external tool 配置 `colcon build`

选择 **Settings | Tools | External Tools** （或者 shift x 2) 使用 **+** 添加一个新的 external tool，配置如下：
 ![在这里插入图片描述](https://img-blog.csdnimg.cn/d688b5162c5345109b05c684dfd0d818.png)

## 3.2 开始编译 dev_ws 工程

使用 tool bar 或者 shift x 2 (external tool)，开始编译 dev_ws 工程。
 ![在这里插入图片描述](https://img-blog.csdnimg.cn/16c7c4f1f9814c39aa149fa660d2f44a.png)
 ![在这里插入图片描述](https://img-blog.csdnimg.cn/5298c08058a947cab8ab3a778a5f613b.png)

## 3.3 编译结果：

可以使用 红框中的图标进行 rebuild。
 ![img](https://img-blog.csdnimg.cn/13f36a67d5614c788636ea009770899d.png)

需要 **reload compilation database**

![在这里插入图片描述](https://img-blog.csdnimg.cn/d4488676edb34f45a67c2acc09dbadd0.png)

其实这一步也是为了生成 `compile_commands.json`，做完这一步之后可以跳至 2。

**编译完整个 ros2 `dev_ws` 工程之后，是无法调试单独的 ros2 `package` 的，需要对每一个 ros2 `package` 进行配置，从而进行调试。**

------

# 4. 调试单独的 ros2 package

大致步骤：

1. 创建 ros2 package 的独立的 colcon build bash 脚本。
2. 创建 clion 中 ros2 package 的独立的 custom build target。
3. 创建 clion 中 对独立的 custom build target 的 **run | debug** configuration。
4. 构建（build） ros2 package。
5. 调试（debug）ros2 package。

## 4.1 创建 ros2 package 的独立的 colcon build bash 脚本

在 `cpp_pubsub` 目录下，创建 `cmake_commands.bat`。

```bash
jacob@jacob-xx15:~/Work_ROS/ros2_study_ws/dev_ws/src/cpp_pubsub$ tree
.
├── cmake_commands.bat  # 这玩意
├── CMakeLists.txt
├── include
│   └── cpp_pubsub
├── package.xml
└── src├── publisher_member_function.cpp└── subscriber_member_function.cpp3 directories, 5 files
```

编写 pubsub package 的 cmake_commands.bat，其他 package 可以参考这个来写。
 第一行：构建命令。
 第二行：编译命令。
 第三行：安装命令。



```bash
/usr/bin/cmake /home/jacob/Work_ROS/ros2_study_ws/dev_ws/src/cpp_pubsub -DCMAKE_EXPORT_COMPILE_COMMANDS=ON -G Ninja -DCMAKE_INSTALL_PREFIX=/home/jacob/Work_ROS/ros2_study_ws/dev_ws/install/cpp_pubsub
/usr/bin/cmake --build /home/jacob/Work_ROS/ros2_study_ws/dev_ws/build/cpp_pubsub -- -j8 -l8
/usr/bin/cmake --install /home/jacob/Work_ROS/ros2_study_ws/dev_ws/build/cpp_pubsub
```

## 4.2 创建 clion 中 ros2 package 的独立的 custom build target

1. 在clion 中打开 **custom build targets** 窗口（shift x 2 输入 custom build targets）。
2. 按照截图流程进行配置（edit tool 中的 cmake_commands.bat 就是步骤1中创建的文件）。
   ![在这里插入图片描述](https://img-blog.csdnimg.cn/e74ea8f3b28146eaa48f084d2b94dc32.png)

## 4.3 创建 clion 中 对独立的 custom build target 的 **run | debug** configuration

按照截图流程进行操作
 其中 5 中的 target 就是 步骤 2 中创建的 **custom build target** for **pubsub package**。
 如果 6 中有 build 文件夹，可以手动清除。
 ![在这里插入图片描述](https://img-blog.csdnimg.cn/5c80e75757d64538af8e9e3b482ccb62.png)

## 4.4 构建（build） ros2 package

可以先删除 `dev_ws/build/cpp_pubsub/` 目录中所有文件，在用 clion 进行编译之前。
 按照截图进行 pubsub package 的编译。
 ![在这里插入图片描述](https://img-blog.csdnimg.cn/10009ad6e8224bd8af0d94f4926d30d1.png)
 编译结果：

## 4.5 调试（debug）ros2 package

由于代码的特殊信，当前 package 包含两个可执行程序 pub（talker），sub（listener）。要调试两者的功能时，需要按截图操作。
 如 2 中所示，需要为 pub，sub 分别创建调试配置，在 5 中选择 `dev_ws/build/cpp_pubsub` 可执行程序（talker，listener）。
 ![在这里插入图片描述](https://img-blog.csdnimg.cn/ce4766ee997e4dbea1b1e72a36446c06.png)
 调试结果：pub:
 ![在这里插入图片描述](https://img-blog.csdnimg.cn/e0d1ce45b12b4ad1af54d18071651e10.png)
 调试结果：sub:
 ![在这里插入图片描述](https://img-blog.csdnimg.cn/9b770f9caecb4bc3b642aebc65f1c07e.png)