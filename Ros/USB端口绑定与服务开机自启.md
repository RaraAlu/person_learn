#### 根据USB设备绑定端口

**需求：**Ubuntu系统中现接入雷达和智能车底盘两台USB设备，请为二者绑定端口。

**实现原理：**可以通过USB设备本身的“标识”实现端口绑定。

**流程如下：**

（1）.查找设备idVendor和idProduct

接入两个USB设备，在终端调用指令`lsusb`查看显示系统中以及连接到系统的USB设备信息。

![img](file:///home/ls/%E6%96%87%E6%A1%A3/_book/assets/1.2.2_01lsusb%E6%9F%A5%E7%9C%8BUSB%E8%AE%BE%E5%A4%87%E4%BF%A1%E6%81%AF.PNG)

如上图所示，红色方框内数据为两台USB设备的idVendor和idProduct（两个参数之间使用”：“分隔）。

另外：可以通过重新插拔比较的方式确定哪些数据对应接入的USB设备。

（2）.编写映射规则

在`/etc/udev/rule.d`目录下新建文件xxx.rules（文件名自定义），输入如下内容：

```shell
KERNEL=="ttyUSB*", ATTRS{idVendor}=="10c4", ATTRS{idProduct}=="ea60", MODE:="0777", SYMLINK+="mylidar"
KERNEL=="ttyUSB*", ATTRS{idVendor}=="1a86", ATTRS{idProduct}=="7523", MODE:="0777", SYMLINK+="mycar"
```

代码解释：

- KERNEL是内核固定名称，这里统一是“ttyUSB*”；
- MODE是节点权限，通常改为“0777”，表示可读写可运行；
- SYMLINK是符号连接，即绑定的别名；
- ATTRS是设备厂商的唯一标识，idVendor和idProduct正好组成上面通过lsusb查找到的设备ID。

***小提示：**一般的USB设备供应商都会提供类似的脚本文件，对于调用者而言，直接复制该文件到*`/etc/udev/rule.d`*目录即可。*

（3）.使规则生效

在终端下输入如下指令：

```shell
sudo service udev reload
sudo service udev restart
```

再重新插拔设备即可。

（4）.测试

终端下输入如下指令`ll /dev | grep ttyUSB`，运行结果如下：

![img](file:///home/ls/%E6%96%87%E6%A1%A3/_book/assets/1.2.2_02%E6%98%A0%E5%B0%84%E7%BB%93%E6%9E%9C.PNG)

也可以多次插拔USB设备，会发现设备端口`ttyUSBn`中的n编号会变动，但是别名是始终可以指向对应的USB设备的。至此，就可以使用别名来关联所需使用的USB设备了。

**缺点：**

上述实现也存在一定的局限性，当Ubuntu接入两台或多台相同型号的USB设备时，由于设备ID是一样的，该种实现方式只会对其中的一台设备生效，这种情况下，就需要通过第二种策略来实现端口的绑定了。

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

```shell
KERNEL=="ttyUSB*", KERNELS=="1-1.3:1.0", MODE:="0777", SYMLINK+="rplidar_front"
KERNEL=="ttyUSB*", KERNELS=="1-1.4:1.0", MODE:="0777", SYMLINK+="rplidar_back"
```

（3）.使规则生效

在终端下输入如下指令：

```shell
sudo service udev reload
sudo service udev restart
```

再重新插拔设备即可。

（4）.测试

终端下输入如下指令`ll /dev | grep ttyUSB`，运行结果如下：

![img](file:///home/ls/%E6%96%87%E6%A1%A3/_book/assets/1.2.2_05%E6%98%A0%E5%B0%84%E7%BB%93%E6%9E%9C.PNG)

至此，就可以使用别名来关联所需使用的USB设备了。

**缺点：**USB设备必须连接在主机的指定端口上，否则，会导致端口绑定失败，或产生逻辑错误。

#### 3.其他说明

并非所有的USB设备端口号都是`ttyUSBn`的格式，比如Arduino的端口号可能是`ttyACMn`，而对于USB摄像头而言，一台设备则对应两个端口号，分别是`videon`和`video(n+1)`，并且启用摄像头设备一般使用的是`videon`端口，绑定时需要关联的也是该接口。

但是无论是外接何种USB设备，也不管采用上述两种方案的哪一种进行端口绑定，其原理都是类似的，只是实现细节不同而已。如果是外接的Arduino设备，那么需要在rules文件中的将`KERNEL=="ttyUSB*"`修改为`KERNEL=="ttyACM*"`，如果外接的是USB摄像头，那么则需要在rules文件中的将`KERNEL=="ttyUSB*"`修改为类似于`KERNEL=="video[0,2,4,6]"`的格式，其中`video[0,2,4,6]`表示可以绑定的端口为`video0`或`video2`或`video4`或`video6`。



## 关于自启动服务的说明

1. Micro_Ros  serial自启动
2. al_server service_go 自启动
3. gphpr_pub gphpr_node自启动
4. fishbot_bringup fishbot_bringup自启动
5. my_exer05_action_server nav_client 自动启‘

在用户根目录下创建 `Start_sev`,新建文件`bot_boot.sh`

```shell
#!/bin/bash

# 替换以下内容为您的实际 ROS 2 版本和工作空间路径
source /opt/ros/humble/setup.bash
source /home/ls/bh_ws_t/install/setup.bash

# 启动您的ROS节点
ros2 run gphpr_pub gphpr_node


```

在`/etc/systemed/system/`中新建`bot_boot.service`

```shell
[Unit]
Description=Gphpr Node Service
After=network.target

[Service]
Type=simple
User=ls
ExecStart=/home/ls/Start_sev/start_gphpr_node.sh
Restart=on-failure
RestartSec=3s

[Install]
WantedBy=multi-user.target

```

这是在 Linux 系统（如 Ubuntu）中用来配置服务自启动的常用方法。我将逐一解释这个文件的各个部分：

### [Unit] 部分

- `Description`: 服务的描述，例如 "Gphpr Node Service"。
- `After`: 指定服务依赖的其他服务。这里指定了 `network.target`，意味着该服务将在网络服务之后启动。

### [Service] 部分

- `Type=simple`: 指定服务的启动类型。`simple` 是最常用的类型，表示一旦启动命令执行，服务即被认为已启动。
- `User=ls`: 指定运行服务的用户，这里是用户 `ls`。
- `ExecStart`: 指定启动服务时执行的命令，这里是运行 `/home/ls/Start_sev/start_gphpr_node.sh` 脚本。
- `Restart=on-failure`: 指定服务失败时的重启策略。在这种情况下，如果服务异常退出（失败），则会尝试重新启动它。
- `RestartSec=3s`: 在尝试重启服务前等待的时间。在这里设置为3秒。

### [Install] 部分

- `WantedBy=multi-user.target`: 指定该服务应该在哪个 "target" 下启动。`multi-user.target` 是一个多用户图形界面环境。

### 总结

这个服务配置的目的是在系统启动时自动启动 `start_gphpr_node.sh` 脚本，并在脚本失败时尝试重新启动。该服务在网络服务启动之后启动，运行在用户 `ls` 的权限下。

请注意，要使服务能够在启动时自动运行，您需要通过 `systemctl enable [服务名]` 命令启用该服务。此外，确保脚本文件具有执行权限，您可以通过运行 `chmod +x /home/ls/Start_sev/start_gphpr_node.sh` 来设置。
