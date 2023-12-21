下面是在Ubuntu 22.04上从源代码编译安装OpenCV 4.5.4的步骤：

1. **安装必要的依赖项**：

```shell
sudo apt update && sudo apt install -y \
    build-essential cmake git pkg-config libgtk-3-dev \
    libavcodec-dev libavformat-dev libswscale-dev libv4l-dev \
    libxvidcore-dev libx264-dev libjpeg-dev libpng-dev libtiff-dev \
    gfortran openexr libatlas-base-dev python3-dev python3-numpy \
    libtbb2 libtbb-dev libdc1394-22-dev libopenexr-dev \
    libgstreamer-plugins-base1.0-dev libgstreamer1.0-dev
```

1. **克隆OpenCV和OpenCV Contrib仓库**：

```shell
mkdir ~/opencv_build && cd ~/opencv_build
git clone -b 4.5.4 https://gitee.com/opencv/opencv.git
git clone -b 4.5.4 https://gite.com/opencv/opencv_contrib.git
```

1. **准备构建环境**：

```shell
cd ~/opencv_build/opencv
mkdir build && cd build
```

1. **配置CMake**：

```shell
cmake -D CMAKE_BUILD_TYPE=RELEASE \
      -D CMAKE_INSTALL_PREFIX=/usr/local \
      -D INSTALL_C_EXAMPLES=ON \
      -D INSTALL_PYTHON_EXAMPLES=ON \
      -D OPENCV_GENERATE_PKGCONFIG=ON \
      -D OPENCV_EXTRA_MODULES_PATH=~/opencv_build/opencv_contrib/modules \
      -D BUILD_EXAMPLES=ON ..
```

这个命令会配置构建系统，并指定包括例子和额外模块的安装。

1. **编译OpenCV**：

```shell
make -j$(nproc)
```

这将开始编译过程，`$(nproc)` 将使用所有可用的处理器核心来加速编译过程。

1. **安装OpenCV**：

```shell
sudo make install
sudo ldconfig
```

此步骤会将编译好的OpenCV安装到您的系统中。

完成这些步骤后，OpenCV 4.5.4应该就安装在您的系统上了。您可以通过执行 `pkg-config --modversion opencv4` 来检查安装的版本。在Python中，您可以验证安装的版本如下：

```shell
python
import cv2
print(cv2.__version__)
```