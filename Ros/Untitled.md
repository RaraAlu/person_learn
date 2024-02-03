#### 根据USB设备绑定端口

1）.查找设备idVendor和idProduct

接入两个USB设备，在终端调用指令`lsusb`查看显示系统中以及连接到系统的USB设备信息。

![img](file:///home/ls/%E6%96%87%E6%A1%A3/_book/assets/1.2.2_01lsusb%E6%9F%A5%E7%9C%8BUSB%E8%AE%BE%E5%A4%87%E4%BF%A1%E6%81%AF.PNG)

如上图所示，红色方框内数据为两台USB设备的idVendor和idProduct（两个参数之间使用”：“分隔）。

另外：可以通过重新插拔比较的方式确定哪些数据对应接入的USB设备。

（2）.编写映射规则

在`/etc/udev/rule.d`目录下新建文件xxx.rules（文件名自定义），输入如下内容：

```shell
KERNEL=="ttyUSB*", ATTRS{idVendor}=="10c4", ATTRS{idProduct}=="ea60", MODE:="0777", SYMLINK+="mylidar"
KERNEL=="ttyUSB*", ATTRS{idVendor}=="1a86", ATTRS{idProduct}=="7523", MODE:="0777", SYMLINK+="
```

代码解释：

- KERNEL是内核固定名称，这里统一是“ttyUSB*”；
- MODE是节点权限，通常改为“0777”，表示可读写可运行；
- SYMLINK是符号连接，即绑定的别名；
- ATTRS是设备厂商的唯一标识，idVendor和idProduct正好组成上面通过lsusb查找到的设备ID。

（3）.使规则生效

在终端下输入如下指令：

```shell
sudo service udev reload
sudo service udev restart
```

（4）.测试

终端下输入如下指令`ll /dev | grep ttyUSB`，运行结果如下：![img](file:///home/ls/%E6%96%87%E6%A1%A3/_book/assets/1.2.2_02%E6%98%A0%E5%B0%84%E7%BB%93%E6%9E%9C.PNG)

#### 2.根据主机硬件绑定端口

**需求：**无人车中现接入一前一后两台相同型号的雷达，请为二者绑定端口。

**实现原理：**USB设备所连接主机的USB接口也是有其“标识”的，可以通过这个标识实现端口绑定。

**流程如下：**

（1）.查看所连接的主机USB接口的KERNELS

调用如下指令查看第一台雷达的USB信息：

```shell
udevadm info --attribute-walk --name=/dev/ttyUSB0 | grep KERNELS
```

![img](file:///home/ls/%E6%96%87%E6%A1%A3/_book/assets/1.2.2_03%E6%9F%A5%E7%9C%8BUSB0%E7%9A%84KERNELS.PNG)

调用如下指令查看第二台雷达的USB信息：

```shell
udevadm info --attribute-walk --name=/dev/ttyUSB1 | grep KERNELS
```

![img](file:///home/ls/%E6%96%87%E6%A1%A3/_book/assets/1.2.2_04%E6%9F%A5%E7%9C%8BUSB1%E7%9A%84KERNELS.PNG)

运行结果相比较不同的KERNELS，第一台雷达端口地址为`KERNELS==1-1.3:1.0`，第二个雷达端口地址为`KERNELS==1-1.4:1.0`。可以使用此数据作为不同端口的“唯一性标识”。

（2）.编写映射规则

在`/etc/udev/rule.d`目录下新建文件xxx.rules（文件名自定义），输入如下内容：

```
KERNEL=="ttyUSB*", KERNELS=="1-1.3:1.0", MODE:="0777", SYMLINK+="rplidar_front"
KERNEL=="ttyUSB*", KERNELS=="1-1.4:1.0", MODE:="0777", SYMLINK+="rplidar_back"
```

（3）.使规则生效

在终端下输入如下指令：

```
sudo service udev reload
sudo service udev restart
```

再重新插拔设备即可。

（4）.测试

终端下输入如下指令`ll /dev | grep ttyUSB`，运行结果如下：![img](file:///home/ls/%E6%96%87%E6%A1%A3/_book/assets/1.2.2_05%E6%98%A0%E5%B0%84%E7%BB%93%E6%9E%9C.PNG)

至此，就可以使用别名来关联所需使用的USB设备了。

**缺点：**USB设备必须连接在主机的指定端口上，否则，会导致端口绑定失败，或产生逻辑错误。

#### 3.其他说明

并非所有的USB设备端口号都是`ttyUSBn`的格式，比如Arduino的端口号可能是`ttyACMn`，而对于USB摄像头而言，一台设备则对应两个端口号，分别是`videon`和`video(n+1)`，并且启用摄像头设备一般使用的是`videon`端口，绑定时需要关联的也是该接口。

但是无论是外接何种USB设备，也不管采用上述两种方案的哪一种进行端口绑定，其原理都是类似的，只是实现细节不同而已。如果是外接的Arduino设备，那么需要在rules文件中的将`KERNEL=="ttyUSB*"`修改为`KERNEL=="ttyACM*"`，如果外接的是USB摄像头，那么则需要在rules文件中的将`KERNEL=="ttyUSB*"`修改为类似于`KERNEL=="video[0,2,4,6]"`的格式，其中`video[0,2,4,6]`表示可以绑定的端口为`video0`或`video2`或`video4`或`video6`。