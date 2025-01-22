- [性能要点](#性能要点)
- [Tools（工具使用）](#tools工具使用)
  - [模拟器与手机测试（FrameDebug \&\& Profiler）](#模拟器与手机测试framedebug--profiler)
    - [安卓](#安卓)
      - [安卓设备](#安卓设备)
        - [安卓模拟器](#安卓模拟器)
        - [安卓手机](#安卓手机)
      - [ADB连接（连接安卓设备）](#adb连接连接安卓设备)
        - [adb.exe直连](#adbexe直连)
        - [Android Studio连接](#android-studio连接)
        - [找不到设备重连](#找不到设备重连)
      - [Unity Profiler连接](#unity-profiler连接)
        - [ADB连接（连接Unity Profiler）](#adb连接连接unity-profiler)
        - [Wifi连接](#wifi连接)
      - [Unity Profiler调试](#unity-profiler调试)
- [Optimization（性能分析）](#optimization性能分析)
  - [UGUI](#ugui)
    - [UGUI.Rendering.UpdateBatches](#uguirenderingupdatebatches)
      - [Canvas.SendWillRenderCanvases()](#canvassendwillrendercanvases)
        - [Render](#render)
        - [Layout](#layout)
      - [Canvas.BuildBatch](#canvasbuildbatch)
    - [UGUI.Rendering.RenderSubBatch](#uguirenderingrendersubbatch)
    - [物体显隐](#物体显隐)
      - [GameObject.SetActive](#gameobjectsetactive)
      - [CanvasGroup.alpha](#canvasgroupalpha)
      - [将物体移出画布范围](#将物体移出画布范围)

# 性能要点
[UWA发布 | Unity手游性能年度蓝皮书](https://community.uwa4d.com/blog/detail?id=1801188355492564993&entrance=0)

# Tools（工具使用）

## 模拟器与手机测试（FrameDebug && Profiler）

### 安卓

#### 安卓设备

##### 安卓模拟器
打开模拟器，并将打包好的游戏apk拖到模拟器主界面，模拟器会自动进行安装
![alt text](assets/unity_profiler/image-7.png)
双击运行游戏
![alt text](assets/unity_profiler/image-10.png)

##### 安卓手机
用USB连接电脑，将游戏apk拖入手机，双击进行安装
双击进行游戏

#### ADB连接（连接安卓设备）

##### adb.exe直连
打开Unity，找到安装的Android SDK路径（Edit -> Preferences -> External Tools -> Android SDK Tools Installed with Unity）
![alt text](assets/unity_profiler/image-8.png)
打开Android SDK路径，接着打开路径下的platform-tools目录，可以看到目录下有adb.exe文件
![alt text](assets/unity_profiler/image-9.png)
在当前路径下开启cmd命令行
![alt text](assets/unity_profiler/image-11.png)
确认当前连上的设备 adb devices
![alt text](assets/unity_profiler/image-15.png)

##### Android Studio连接
打开Android Studio
![alt text](assets/unity_profiler/image-23.png)
确认ADB安装
点开Setting，找到Android SDK Platform-Tools选项，如未安装则先进行安装
![alt text](assets/unity_profiler/image-22.png)
确认ADB路径
如上窗口，找到Android SDK Location，如下图为C:\Users\BBGame\AppData\Local\Android\Sdk，ADB则在路径下的platform-tools\adb，完整路径也就是C:\Users\BBGame\AppData\Local\Android\Sdk\platform-tools\adb
![alt text](assets/unity_profiler/image-24.png)
打开输入终端Terminal
![alt text](assets/unity_profiler/image-25.png)
![alt text](assets/unity_profiler/image-26.png)
确认当前连接设备
输入adb命令（adb完整路径）+ devices，C:\Users\BBGame\AppData\Local\Android\Sdk\platform-tools\adb devices
![alt text](assets/unity_profiler/image-28.png)

##### 找不到设备重连
首先关闭adb调式服务器 adb kill-server
![alt text](assets/unity_profiler/image-12.png)
再开启adb调试服务器 adb start-server（看到started successfully意为成功）
![alt text](assets/unity_profiler/image-13.png)

#### Unity Profiler连接

##### ADB连接（连接Unity Profiler）
连接Unity端口 adb forward tcp:xxxxx localabstract:Unity-yyy
xxxxx是Unity连接的端口，可以在Profiler里点击查看，这里为34999 
![alt text](assets/unity_profiler/image-16.png)
yyy是游戏的PackageName，可以从Edit->Project Settings->Player->Other Settings->Identification->Package Name查看，这里为com.bbgame.yakuza.internal
![alt text](assets/unity_profiler/image-17.png)
如果设备多于1个，需要指定要连接哪个设备 在命令adb后加入 -s xxx
xxx是设备名，也就是上面确认到当前连上的设备，这里为emulator-5554
最终命令为adb -s emulator-5554 forward tcp:34999 localabstract:Unity-com.bbgame.yakuza.internal
![alt text](assets/unity_profiler/image-18.png)
打开profiler，选中AndroidPlayer，如果帧数正常增加则连接成功
![alt text](assets/unity_profiler/image-19.png)

##### Wifi连接
获取当前设备所接wifi的ip地址，手机的话可以在设置、WLAN中打开查看
打开Unity Profiler，选中<Enter IP>选项，输入设备wifi的ip地址，点击Connect
![alt text](assets/unity_profiler/image-29.png)
![alt text](assets/unity_profiler/image-30.png)

#### Unity Profiler调试
调试中可以选中红色按钮暂停
![alt text](assets/unity_profiler/image-20.png)
也可以开启FrameDebugger进行调试
![alt text](assets/unity_profiler/image-21.png)

# Optimization（性能分析）
[Unity3D性能优化——工具篇](https://zhuanlan.zhihu.com/p/39529241)
[Unity3D性能优化——CPU篇](https://zhuanlan.zhihu.com/p/39998137)

## UGUI
[Unity UGUI 规范 优化 大全](https://www.cnblogs.com/hiker-online/p/13616255.html)

### UGUI.Rendering.UpdateBatches
Canvas下UGUI物体属性发生变更时一系列消耗
![alt text](assets/unity_profiler/image.png)
![alt text](assets/unity_profiler/image-1.png)
#### Canvas.SendWillRenderCanvases()
对持有UGUI脏标记（Dirty）物体（GameObject）进行ReBuild（重新设置相关属性）
![alt text](assets/unity_profiler/image-2.png)
##### Render
渲染属性变更（材质纹理颜色）
![alt text](assets/unity_profiler/image-3.png)
##### Layout
顶点位置变更（位移缩放）
![alt text](assets/unity_profiler/image-4.png)
#### Canvas.BuildBatch
对持有脏标记物体的Canvas进行Rebatch，合并相同批次物体网格（如果当前帧Canvas下的物体没有变化则不会触发Canvas.BuildBatch）
![alt text](assets/unity_profiler/image-5.png)

### UGUI.Rendering.RenderSubBatch
UGUI渲染开销，Calls（DrawCall越高 耗时越高）
![alt text](assets/unity_profiler/image-6.png)


### 物体显隐
#### GameObject.SetActive
会触发自己及子节点所有Component的OnEnable/OnDisable方法，Component越多，消耗越大
持有继承Graphic的Component（Image/Text）时，会因为Material Dirty触发Canvas.Rebuild，重新设置顶点数据
会造成所在Canvas下网格变动并触发Canvas.Rebatch
SetActive实现是在原生层，有一定消耗

#### CanvasGroup.alpha
会造成所在Canvas下网格变动并触发Canvas.Rebatch
CanvasGroup.alpha实现是在原生层，有一定消耗

#### 将物体移出画布范围
会造成所在Canvas下网格变动并触发Canvas.Rebatch