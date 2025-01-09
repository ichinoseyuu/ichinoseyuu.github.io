---
title: 基于深度学习的交通标识检测
date: 2024-03-28 09:12:24
tags: [-Python, -深度学习]
categories: -发布
---

---

### 更新日志

2024-05-11 -写UI中途发现版本兼容问题，现修正前期安装流程，更新了打包流程
2024-04-07 -新增遇到的问题分栏
2024-04-05 -简单记录了 Anaconda 的基本指令
2024-03-28 -完成了基础的环境搭建记录

---

### 前言

2024-03-28：时间过得很快，大学四年就这么匆匆溜走，最近也是进入了毕设阶段，目前已有一些进展，正好有时间，那就大致记录一下目前的情况吧。  
2024-05-11：这可能是博主第一次接触深度学习领域，也可能是最后一次，希望这博客能对有需要的人有所帮助。
2024-07-16：项目已打包，[点击此处跳转](<https://github.com/ichinoseyuu/yolov6-Traffic_signs>)

---

### 一、环境搭建

#### 下载并安装 Acnconda3

在官网下载Acnconda3，[点击此处跳转](<https://www.anaconda.com>)

#### 查看系统版本支持

![系统版本支持](https://cdn.jsdelivr.net/gh/ichinoseyuu/Image/202403280921933.png)

#### 选择 Tensorflow 版本

![Tensorflow版本](https://cdn.jsdelivr.net/gh/ichinoseyuu/Image/202403280922051.png)

#### 下载 cuda11.0 及 cuDNN8.0，安装 cuda11.0，并将 cuDNN 解压放入 cuda 安装文件夹

#### 检查是否配置成功

```text
####　nvcc -V ##查看安装状态
```

#### 用 Anaconda 配置虚拟环境 Tensorflow24

```text
conda create -n Tensorflow24 python=3.8 ##创建一个虚拟环境
conda activate Tensorflow24  ##进入环境
##安装Tensorflow2.4
pip install tensorflow==2.4.0 -i https://pypi.mirrors.ustc.edu.cn/simple
##安装相同版本的keras
pip install keras==2.4.0 -i https://pypi.mirrors.ustc.edu.cn/simple
```

#### 检查是否安装成功

```text
python                 #进入python环境
import tensorflow as tf    #导入Tensorflow
tf.test.is_gpu_available()   #查看GPU版本信息并返回gpu是否可用
exit()    #退出python环境
```

#### 安装必要的包文件

这些包都需要安装在Tensorflow24环境下。

```text
pip install opencv-python==4.9.0.80 #安装不上可以下载到本地安装
conda install numpy==1.19.5
conda install Pillow=8.4.0
conda install tensorboard=2.7.0
conda install tqdm
Pip install pycocotools
pip install matplotlib=3.2.2
```

#### 从 github 将 Tensorflow-yoloV6 项目克隆到本地

该项目是基于 bubbliiiing大佬的项目编写的，大部分保留了源代码。 [点击此处跳转](<https://github.com/bubbliiiing/yolox-tf2>)
![github相关仓库](https://cdn.jsdelivr.net/gh/ichinoseyuu/Image/202403280931166.png)

#### 数据准备和处理

自己做数据集，准备足够的带有交通标识的图片，然后在 Anaconda 里下载输入 pip install labelimg 命令下载安装 Labelimg。
重要：使用该软件进行数据集的标注，在使用时会有闪退报错的情况，提示传输的数据类型不正确，根据报错提示到指定文件的指定代码处进行修改，对预传输的数据进行强制转换成需要的数据类型即可。

使用公开的数据集，公开的数据集可能需要转换格式，需要自行编写代码转化成 VOC 格式，并放到指定目录，公开数据集的标注不一定适配想要的效果，建议还是自行准备数据集。

#### 初步训练

打开 VScode，用搭建的 Anaconda 环境运行 train.py。

训练时长跟gpu核心数、数据集的大小、训练的世代次数有关，数据集的大小决定每个训练世代的时长，模型参数则可以设置训练的世代次数，训练的世代次数越多，训练时长也就越长，我的数据集花了 8 个小时去训练。

---

### 二、PyQt5

#### 常见 GUI 框架

PyQt5：Qt 是一个跨平台的 C++图形用于界面库。QT 一度被诺基亚使用，后出售芬兰的软件公司 Digia Oyj。PyQt5 是基于 Digia 公司 Qt5 的 Python 接口，由一组 Python 模块构成。PyQt5 本身拥有超过 620 个类和 6000 函数及方法。在可以运行于多个平台，包括：Unix，Windows，Mac OS。

Pyside6：Pyside 是 QT 公司官方提供 Python 包，上一版本为 Pyside2，对应的是 QT5，最新版命名规则进行了调整，更改为 Pyside6，对应的是 QT6 版本。由于官方出品的比较看好，缺点是发布比较晚，资料没有 PyQt5 多。

Tkinter：Python 内置的 GUI 框架，使用 TCL 实现，Python 中内嵌了 TCL 解释器，使用它的时候不用安装额外的扩展包，直接 import，跨平台。不足之处在于 UI 布局全靠代码实现，只有 15 种常用部件，显示效果简陋。

<font color = LightSeaGreen>编写UI界面需要一定的面向对象编程基础，理解程序设计模式。
</font>  

#### 在 Anaconda 下安装 pyqt5

```text
pip3 install pyqt5
pip3 install pyqt5-tools
```

#### 安装 Qt Designer 并打开

方法一：安装 Anaconda3 时会自动安装，在Anaconda3输入命令就能打开  
注意：需要在添加环境变量，否则无法在 conda 环境下调用，只能手动打开。

方法二：去 GitHub 开源项目地址下载。

[项目地址](<https://github.com/PyQt5/QtDesigner/releases/tag/5.15.0>)

#### Qt Designer 的简单使用

CSDN 上有许多博主写有相关教程，B 站上有许多 up 主出过很多相关视频

Qt Designer 里面的控件界面编写需要前端基础，有 CSS 基础的可以很快上手，没有基础的也可以通过查看官方文档去查看控件属性，然后在编写样式。

[QT官方文档](<https://doc.qt.io/qt-6/stylesheet-reference.html>)

#### 实现UI界面的功能

在Qt Designer将界面设计完成后，在vscode中输入指令将.ui文件转化成.py文件，转换完成后就会生成一个窗口类，然后新建一个python文件作为程序的入口，并继承这个窗口类，代码请参考项目具体内容

```text
pyuic5 -x fileName.ui -o fileName.py ##将.ui文件转化成.py文件
```

---

### python 程序打包

#### 安装 pyinstaller

在Tensorflow24环境中安装pyinstaller

```text
pip pyinstaller 
```

#### 打包项目

在vscode中执行以下命令

```text
pyinstaller -D -c flieName.py ##生成带命令行窗口的文件夹包
pyinstaller -D -w flieName.py ##生成不带命令行窗口的文件夹包
pyinstaller --help ##查看pyinstaller的帮助文档
```

### Anaconda 常用指令

```text
conda info -e    ##查看所有环境
conda create --name myenv  ##创建一个新环境
conda activate myenv   ##激活一个环境
conda install package_name=version ##安装包到环境中
conda update conda   ##更新 Conda
conda update --all   ##已安装的包
conda list    ##查看当前环境下已安装的包
conda search package_name  ##查找可用包的信息
conda remove package_name  ##删除一个包
conda env remove --name myenv  ##删除一个环境
conda --help ##查看anaconda的帮助文档
pip install /path/to/package.whl  ##安装本地包
```

---
