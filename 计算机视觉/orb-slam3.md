## 前置工作

**DBoW2的安装步骤：**

1. 克隆DBoW2的Git仓库：

```
git clone https://github.com/dorian3d/DBoW2.git
```

编译并安装DBoW2：

1. ```shell
   cd DBoW2
   mkdir build
   cd build
   cmake ..
   make -j$(nproc)
   sudo make install
   ```

这将编译DBoW2并将其安装到默认的系统位置，通常在`/usr/local/`下。

**g2o的安装步骤：**

1. 克隆g2o的Git仓库：

```shell
git clone https://github.com/RainerKuemmerle/g2o.git
```

编译并安装g2o：

1. ```shell
   cd g2o
   mkdir build
   cd build
   cmake ..
   make -j$(nproc)
   sudo make install
   ```

这同样会编译g2o并将其安装到默认的系统位置。

**注意**：

- `-j$(nproc)`参数告诉`make`工具同时使用所有的CPU核心来编译，这可以显著加快编译过程。如果你想要指定使用的核心数，可以将`$(nproc)`替换为你希望使用的数字。
- 如果你没有管理员权限或者想要安装到非标准位置，你可以使用`cmake`的`-DCMAKE_INSTALL_PREFIX`参数来指定安装目录。
- 安装后，确保你的`LD_LIBRARY_PATH`和`PKG_CONFIG_PATH`环境变量包含了库文件和pkg-config文件的路径，这样编译其他依赖于DBoW2和g2o的项目时能够正确找到它们。

例如，如果你安装到`/home/user/local`目录，你需要将以下内容添加到你的`.bashrc`或者`.bash_profile`中：

```shell
export LD_LIBRARY_PATH=/home/user/local/lib:$LD_LIBRARY_PATH
export PKG_CONFIG_PATH=/home/user/local/lib/pkgconfig:$PKG_CONFIG_PATH
```