---
title: 利用DSP实现数字滤波器
date: 2023-09-07 19:07:39
description: 利用DSP原理设计带通滤波器，实现音频去噪
categories: 教程
top_img: https://cdn.jsdelivr.net/gh/ichinoseyuu/Image/202412041418031.png
cover: https://cdn.jsdelivr.net/gh/ichinoseyuu/Image/202412041418031.png
tags: 
    - Matlab
    - CCS
---

---

### 前言

大学的最后一个课程设计终于是完成了，虽然实际耗费的时间不到 10 小时（中间一直在摸鱼），不打算考研甚至有点闲得无聊，一时头脑发热想写下这篇博客。
笔者主要使用的软件是 CCSv2 版本和 Matlab2021，使用不同版本可能会出现不可预知的一些问题。

---

### 主要思路

- 使用 Matlab 对音频进行加噪处理，并将数据输出到.dat 格式文件中。
- 在 CCS 中设计一个滤波器，将 Matlab 导出的数据进行处理，并输出到相应的.dat 文件中。
- 使用 Matlab 读取 CCS 输出的数据与滤波前进行对比。

### 前期准备

- 首先，肯定是安装好所需要的软件。
- 准备一段音频，你可以使用自己录制的音频，也可以使用一首歌曲。
  ps：建议时间不要过长，一句话即可，过长会使 DSP 处理的时间变得很长。

### 使用 Matlab 对音频进行处理

```matlab
Frequency_in = 2500;    %需要引入的干扰正弦波的频率
result_Fs = 8000;   %音频采样率

 %这里是你的音频路径，建议将音频放在与脚本的同级目录下
[data,fs] = audioread('C:\Users\kirit\Documents\MATLAB\CCS_music_filter-main\test_01.mp3');

%=====================以下代码只对一个声道进行处理==========================
temp2 = resample(data,result_Fs,fs); %对原音频再次采样
[size_data,~] = size(temp2);  %求取音频长度

%给音频人为地添加干扰，干扰的频率由Frequency_in给定
%你可以在这里改变干扰的幅度，类型等
noise = 0.01*sin(2*pi*Frequency_in*size_data/result_Fs*linspace(0,1,size_data));

temp2 = temp2 + noise';%加入噪声
temp3 = int32(temp2*10^4);%把数据处理成整形，方便DSP读取

%=====================输出到DSP能读取的文件================================
%打开同级目录下的相应文件，没有则自动新建该文件
fp1 = fopen('_input.dat','wt');

fprintf(fp1,'1651 1 0 0 0\n');%这里是在设置格式

for i = 1:size(temp3)
    %旧方法，输出较大的数会变成指数形式，貌似不能正常读取，我并没有尝试过
    %这里是使用%X来让fprintf来输出16进制
    %fprintf(fp1,'0x%X\n',temp3(i));

    %这里先用dec2hex()函数将数据转成16进制，然后用%s直接输出字符。
    fprintf(fp1, '0x%s\n', dec2hex(temp3(i)));
end

fclose(fp1);%关闭该文件，不关闭可能会导致数据丢失

%sound(temp2,result_Fs); %播放处理结果
%plot(abs(fft(temp2)));%绘制频谱图

```

- 运行后，会在脚本文件的同级目录下生成数据文件。

### CCS 部分

- 配置 DSP 芯片，打开 Setup CCS 2 ('C5000)，选择 C5402 Device Simulator，点击 import，再点击 Save and Quit，然后软件会提示你是否进入 CCS 编辑器，选择是，然后进入 CCS 编辑器主界面。
- 新建项目，在 CCS 菜单栏点击 Project-->New,然后会弹出一个窗口，在窗口中输入项目名（Project Name）、项目存储位置（Location）、项目类型（Project）和目标芯片（Target），对于后两个选项，我们对第一个选择.out 的项目类型，即我们编写汇编项目，目标芯片我们选择 TMS320C54XX，然后点击完成。
- 创建汇编源文件，点击见菜单栏的 File-->New-->Source File,点击弹出的窗口，按下快捷键 ctrl+s 进行保存，在弹出的窗口中选择保存位置、文件名、和文件类型，保存位置建议设置到你的项目目录下；文件名按你的喜好，但要符合命名规则；文件类型我们选择.asm 文件，然后点击保存。
- 将汇编源文件添加到项目中，点击菜单栏的 Project-->Add Files to Project，将你创建的.asm 文件选中，再点击打开。
- 创建链接命令文件，以同样的方法创建一个类型为.cmd 的文件并添加到你的项目中。
- 在项目视图中找到这两个文件并打开，接下来开始编写程序代码。

你的汇编源文件.asm

```text

        .mmregs
        .title  "project4.asm"
        .def   start
      .bss   new_data,1
      .bss   y,1
xn    .usect  "xn",9
a0    .usect  "a0",9

      .data
table:  .word   1*32768/10
       .word   2*32768/10
       .word   3*32768/10
       .word   4*32768/10
       .word   5*32768/10
       .word   4*32768/10
       .word   3*32768/10
       .word   2*32768/10
       .word   1*32768/10

       .text
start: SSBX    FRCT
       STM     #a0,AR1
       RPT     #8
       MVPD    table,*AR1+
       STM     #xn+8,AR2
       STM     #a0+8,AR3
       STM     #9,BK
       STM     #-1,AR0
       LD      #new_data,DP
fir:   RPTZ    A,#8
       MAC     *AR2+0%,*AR3+0%,A
       STH     A,y
       BD      fir
       LD      new_data,B
       STL     B,*AR2+0%
       .end

```

你的链接命令文件.cmd

```text
MEMORY
{
 PAGE 0: EPROM: org=0080h, len=0100h
 PAGE 1: SPRAM: org=0060h, len=0020h
  DARAM: org=0180h, len=0100h
}

SECTIONS
{
 .text : >EPROM PAGE0
 .data : >EPROM PAGE0
 .bss : >SPRAM PAGE1
 xn : >(DARAM align(16) PAGE(1))
 a0 : >(DARAM align(16) PAGE(1))
}
```

- 保存并关闭这两个文件
- 进行汇编，点击 Project-->Build，汇编成功后会在你的项目目录下生成一个 Debug 文件夹，在该文件夹下存在一个.out 类型的文件；点击 File-->Load Program 加载汇编，在弹出的窗口中选中生成的.out 文件，点击打开。
- 配置 I/O 文件前的准备：将 Matlab 中导出的.dat 数据文件，放在项目目录下，并在项目目录下创建一个.txt 的文本文件，用于滤波器输出，打开该文件，并在第一行编写“1651 1 61 1 1”并保存，然后将文件格式改为.dat 格式。
- 配置 I/O 文件，首先在你的汇编源程序文件里的“STH A,y”语句前加探针，并在“ LD new_data,B”语句前加断点和探针。然后点击菜单栏中的 File-->File I/O，在弹出的窗口中点击 Add File，添加 Matlab 导出的.dat 数据文件，将地址（Address）改为"0x0060"，长度（Length）改为"1"，然后点击 Add Probe Point，选中“line 35”，再将 Connect To 选项设置为 Matlab 导出的文件，然后点击 Replace，输入配置完毕，点击确定，然后将选项卡设置为 File Output 以相同的方法添加上一步手动创建的用于滤波器输出.dat 文件，将地址设置为“0x0061”，长度设置为"1"，然后点击 Add Probe Point，选中“line 33”，再将 Connect To 选项设置为用于滤波器输出的文件，然后点击 Replace，输出配置完毕，点击确定，I/O 配置完毕，点击确定即退出 File I/O 窗口。
- 配置图形窗口，点击菜单栏中的 View-->Graph-->Time/Frequency，在的弹出的窗口中将地址改为"0x0060"，Acquisition Buffer Size 改为“1”,Display Data Size 改为“128”，数据类型设置为“16-bit signed integer”，点击确定，以同样的方法，将地址改为“0x0061”，设置输出波形显示窗口。
- 布置好窗口布局，点击 Animate 进行运行，等待程序运行完毕。

### 数据对比

- 将 CCS 项目所输出的.dat 数据文件放到该脚本目录下
- 打开 Matlab，进行数据读取的代码编写

```matlab
%这里是你的.dat数据文件路径
Id = fopen('C:\Users\kirit\Documents\MATLAB\CCS_music_filter-main\_output.dat','r');
data = fscanf(Id,'%x');%按16进制读取
data = data/10^4;%恢复
sound(data,8000); %播放时，注意采样率需要与生成时一致
fclose(Id);
plot(abs(fft(data)));%绘制频谱图
```

- 运行程序，对比原音频和加噪音频，分析效果，反正我听着像对讲机发出的声音。

### 更新

<font color = LightSeaGreen> 2023-09-23 </font>  
课设验收已结束，在这公布我的最终代码以及完整项目。
在此就不提供预览了，需要的话直接下载。
链接：(百度云)<https://pan.baidu.com/s/1PxtIWhfAMpXRFfCYe6G17w?pwd=qrpz>  
(蓝奏云)<https://ichinoseyuu.lanzn.com/iNv3E24lqqni>

<font color = LightSeaGreen> 2023-09-20 </font>

关于大家遇到的一些问题，在这里做出统一回答
Q:Matlab 程序报错，怎么解决？
A:Matlab 程序报错主要是软件的问题，不必非要用和我同样的版本，对于报错怎么解决，以下是我做的三种方案，希望能解决你 Matlab 报错的问题。

```matlab
%最新方案，其实很简单，之前被我想的很复杂，在十进制和十六进制之间纠结
%使用此方案需要注意在CCS中设置I/O文件时需要将文件读取格式改成十进制，而不是十六进制
%至于原因我也不是很理解，AI给了一个我认为相对合理的解释
%对于十六进制数据读取时，我并不能编写出很好的归一化代码，导致了波形失真。
%而对于十进制我恰好能编写出能很好的归一化代码，合理地对音频数据进行缩放、归一化和处理，获得了良好的音频效果。
fprintf(fp1, '%d\n', temp3(i));%在数据导出的for循环中使用该方案

%1.旧方法，输出较大的数会变成指数形式，该方法经验证确实不能正常读取。
%尝试读取过小数的指数形式的数据，读取时的数值都为0
%这里是使用%X来让fprintf来输出16进制
fprintf(fp1,'0x%X\n',temp3(i));

%2.旧方法，滤波后结果很糟糕，跟对讲机一样，发现dsp能直接使用十进制，该方法弃用
%注意该方法在2016版本下不能正常使用
%这里先用dec2hex()函数将数据转成16进制，然后用%s直接输出字符。
fprintf(fp1, '0x%s\n', dec2hex(temp3(i)));
```

Q:CCS 安装上打不开，怎么解决？
A:导致这个问题的原因是 CCS 的后台进程并没有被关掉，导致冲突，启动无响应
解决方法：打开任务管理器，找到后台进程中带 CCS 图标的一个进程，结束该进程。

Q:CCS 正常使用，但是代码报错或警告，怎么解决？
A:报错有很多种情况，以下提供我知道的一些报错的和解决方案。
1、报错，提示 BK 寄存器未定义。
未找到原因，BK 寄存器就是存在的，但是我们可以使用别人汇编过后的项目文件，则不需要去 build，直接进行后面的操作。
2、出现警告，没有报错。
直接忽略，进行后面操作。
3、其他报错。
很有可能是你的操作步骤不对，建议重新打开软件进行再次尝试。

Q:CCS 正常运行，图形不太正确。
A:观察你的\_input.dat 文件： 1.文件是否过小，只有几 kb，如果是这样，你的 Matlab 生成数据的部分出现问题，观察 Matlab 是否报错； 2.文件中的数据是否正常，如果里面的数值全是 0，则说明你的音频选择可能有问题，或者你的 Matlab 生成数据有问题。

---
