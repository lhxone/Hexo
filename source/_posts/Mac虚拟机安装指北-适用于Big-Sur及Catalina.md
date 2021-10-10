---
title: Mac虚拟机安装指北(适用于Big Sur及Catalina)
date: 2021-03-14 10:30:55
tags: 虚拟机
---

# Mac虚拟机安装指北(适用于Big Sur及Catalina)

### 声明
* 本文部分内容转载自CSDN「红叶枫」的博文，遵循CC 4.0 BY-SA版权协议：<a href="https://blog.csdn.net/sinat_30732593/article/details/111305700"><i>原文地址</i></a>
* 本文部分内容转载自CSDN「繁星蓝雨」的博文，遵循CC 4.0 BY-SA版权协议：<a href="https://blog.csdn.net/qq_33375598/article/details/114546699"><i>原文地址</i></a>
* 本文仅作学习交流参考，请支持正版

---

## 所需工具下载地址
<!-- more -->
* ### PD16：适用于M1以及安装了Big Sur系统的Intel芯片Mac
  ><a href="https://pan.baidu.com/s/1H3jB9ZIwKJv-TXFRwL5XKQ">百度网盘下载</a>     提取码：4wod

* ### PD15：适用于未升级至Big Sur的Intel芯片Mac
  ><a href="https://pan.baidu.com/s/1fPiTG67VTgN5Xu-GRKzm6A">百度网盘下载</a>     提取码：e5bs

* ### Windows10 ISO下载
  ><a href="https://www.microsoft.com/zh-cn/software-download/windows10ISO">官网下载</a>
  >
  >ED2K下载(复制并打开迅雷，即可自动下载)
  ```ed2k://|file|cn_windows_10_consumer_editions_version_20h2_updated_feb_2021_x64_dvd_8ddab99d.iso|6223781888|954B729026D6E420EE46FB2DC912F256|/```


---

## M1 Mac以及Big Sur Mac在安装PD16时可能遇到的问题


### 网络初始化失败

<img src="https://img-blog.csdnimg.cn/20201216234951650.png" alt="问题截图">

#### 解决办法：
* 前往 /Library/Preferences/Parallels目录
* 打开network.desktop.xml文件
* 找到```<UseKextless>1</UseKextless>```或```<UseKextless>-1</UseKextless>```, 修改为```<UseKextless>0</UseKextless>```
* 如果找不到这个标记，那么就在根标记下创建它，如下：
```xml
<ParallelsNetworkConfig>
	<!-- 新增 -->
	<UseKextless>0</UseKextless>
</ParallelsNetworkConfig>
```

### USB 问题

#### 解决办法：

* 前往/Library/Preferences/Parallels文件夹
* 打开 dispatcher.desktop.xml文件
* 找到```<Usb>0</Usb>```，修改为```<Usb>1</Usb>```


### 弹窗问题

>进入Windows系统后，可能会出现下面的弹窗


<img src="https://img-blog.csdnimg.cn/20210308200012373.png">

#### 解决办法

1. 下载配置文件：[[下载地址](https://pan.baidu.com/s/1A9G3_yuHFjFqIzN2v-enzA)]   提取码：```c7u0```


2. 打开任务管理器，在进程一栏下拉，找到```Parallels Control Center```，选中该进程，并结束任务


3. 前往目录```C：\ Program Files（x86）\ Parallels \ Parallels Tools \ Plugins \```，并将下载的文件拖入到该文件夹中，在弹出的提示窗口中选择```替换```

4. 重启Windows

