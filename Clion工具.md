[下载 jetbrains-tool] https://www.jetbrains.com/toolbox-app/

对于 Clion 的使用 以及对应 ssh 的使用

分别对应设置中的 工具链 ，部署

需注意的是所有工具链为 Clion默认，Ubuntu为系统默认，可能需要 `sudo apt install cmake`

<img src="C:/Users/%E9%99%88/AppData/Roaming/Typora/typora-user-images/image-20231202013903685.png" alt="image-20231202013903685"  />

![image-20231202014000031](C:/Users/%E9%99%88/AppData/Roaming/Typora/typora-user-images/image-20231202014000031.png)

本地构建一个空项目，然后对应Ubuntu上的一个cmake项目

![image-20231202014047569](C:/Users/%E9%99%88/AppData/Roaming/Typora/typora-user-images/image-20231202014047569.png)

![image-20231202014352411](C:/Users/%E9%99%88/AppData/Roaming/Typora/typora-user-images/image-20231202014352411.png)

上图的选项要勾选，配置的工具链和部署两个选项内的 ssh 名称最好相同 本文这里都是 ls

ubuntu内jbr存在输入法不跟随光标暂无法解决，故应用 ssh