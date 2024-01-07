####  ubuntu 22.04  Lst系统

编译报错

```shell
CMake Error at CMakeLists.txt:21 (find_package):
  By not providing "FindGeographicLib.cmake" in CMAKE_MODULE_PATH this
  project has asked CMake to find a package configuration file provided by
  "GeographicLib", but CMake did not find one.

  Could not find a package configuration file provided by "GeographicLib"
  with any of the following names:

    GeographicLibConfig.cmake
    geographiclib-config.cmake

  Add the installation prefix of "GeographicLib" to CMAKE_PREFIX_PATH or set
  "GeographicLib_DIR" to a directory containing one of the above files.  If
  "GeographicLib" provides a separate development package or SDK, be sure it
  has been installed.


---
```

解决：

1. 安装

```shell
1 sudo apt-get install ros-humble-geographic-*
2 sudo apt-get install geographiclib-*
3 sudo apt-get install libgeographic-*
```

2. 建立软链接
   在ubuntu22.04环境下，安装完package后，cmake编译依然找不到路径。
   这是由于：

```shell
1 The geographiclib-dev installer puts the FindGeographicLib.cmake file in:/usr/share/cmake/geographiclib/FindGeographicLib.cmake
2 The correct location is: /usr/share/cmake-3.16/Modules   
3 Indeed it depends by the version of cmake currently installed. 
```

解决：

```shell
sudo ln -s /usr/share/cmake/geographiclib/FindGeographicLib.cmake /usr/share/cmake-3.22/Modules/
```

再次编译，成功。
