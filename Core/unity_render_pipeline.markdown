- [Render Pipeline（渲染管线）](#render-pipeline渲染管线)
  - [渲染流程](#渲染流程)
    - [渲染阶段](#渲染阶段)
      - [应用阶段](#应用阶段)
        - [流程](#流程)
          - [渲染前准备](#渲染前准备)
          - [上传数据](#上传数据)
          - [设置渲染状态](#设置渲染状态)
          - [设置Shader数据](#设置shader数据)
          - [发起渲染命令](#发起渲染命令)
        - [其余](#其余)
          - [命令缓冲（Command）](#命令缓冲command)
      - [几何阶段](#几何阶段)
      - [光栅化阶段](#光栅化阶段)
  - [Unity渲染管线](#unity渲染管线)
    - [Built-in Render Pipeline（内置渲染管线）](#built-in-render-pipeline内置渲染管线)
    - [URP（Universal Render Pipeline 通用渲染管线）](#urpuniversal-render-pipeline-通用渲染管线)
    - [SRP(Scriptable Render Pipeline 可编程渲染管线)](#srpscriptable-render-pipeline-可编程渲染管线)
  - [渲染管线属性](#渲染管线属性)
    - [Depth Texture（深度图）](#depth-texture深度图)
    - [Opaque Texture（截屏快照）](#opaque-texture截屏快照)
      - [Opaque Downsampling（截屏快照采样模式）](#opaque-downsampling截屏快照采样模式)
      - [Terrain Holes（地形孔洞）](#terrain-holes地形孔洞)
      - [SRP Batcher（SRP合批）](#srp-batchersrp合批)
    - [Dynamic Range（动态范围）](#dynamic-range动态范围)
      - [SDR（Standard Dynamic Range 低动态范围）](#sdrstandard-dynamic-range-低动态范围)
      - [HDR（High Dynamic Range 高动态范围）](#hdrhigh-dynamic-range-高动态范围)
        - [HDR Rendering](#hdr-rendering)
        - [HDR Output](#hdr-output)
        - [Tonemapping](#tonemapping)
  - [渲染顺序](#渲染顺序)
  - [渲染优化](#渲染优化)
    - [批处理](#批处理)
      - [静态合批（static batch）](#静态合批static-batch)
        - [定义](#定义)
        - [优点](#优点)
        - [缺点](#缺点)
        - [合批限制](#合批限制)
        - [开启方式](#开启方式)
  - [GPU](#gpu)
    - [Constant Buffer（CBUFFER 常量缓冲区）](#constant-buffercbuffer-常量缓冲区)

# Render Pipeline（渲染管线）
将场景物体渲染到屏幕上的操作（组合顶点纹理等信息，最后形成一张二维图像）
 [GPU 渲染管线与着色器 大白话总结](https://blog.csdn.net/newchenxf/article/details/119803489)
 [渲染管线概述](https://zhuanlan.zhihu.com/p/508280548?utm_id=0)

## 渲染流程


### 渲染阶段
一般分为3个阶段，应用阶段、几何阶段、光栅化阶段

#### 应用阶段

##### 流程

###### 渲染前准备
剔除相机外的物体部分
###### 上传数据
将物体顶点（位置、纹理坐标、颜色、法线等）从硬盘加载到内存，再从内存加载到显存（GPU不能直接访问内存数据），之后从内存中移除这部分数据（某些之后CPU还需要用到的数据则保留）
###### 设置渲染状态
设置上传的顶点数据在GPU中会如何被渲染（顶点着色器、片段着色器等）
###### 设置Shader数据
贴图、相机位置、光源属性等
###### 发起渲染命令
对GPU提交可以渲染某个图元列表的指令（Drawcall），之后GPU就会根据当前设置的渲染状态对对应图元（顶点）执行渲染操作

##### 其余

###### 命令缓冲（Command）
执行渲染时，CPU提交给GPU的指令都存放在命令缓冲中（Command Buffer），GPU则会不停的从缓冲中提取当前指令并执行
![Alt text](assets/unity_render_pipeline/image-3.png)

#### 几何阶段



#### 光栅化阶段





## Unity渲染管线
![Alt text](assets/unity_render_pipeline/image.png)

### Built-in Render Pipeline（内置渲染管线）

### URP（Universal Render Pipeline 通用渲染管线）

### SRP(Scriptable Render Pipeline 可编程渲染管线)


## 渲染管线属性

### Depth Texture（深度图）
[【Unity】深度图（Depth Texture）的简单介绍](https://zhuanlan.zhihu.com/p/389971233)
深度图会记载相机渲染物体的深度信息，范围从0-1（相机最近到最远），只占用一个颜色通道
深度图信息会在物体进行渲染前先进行记录
创建深度图会占用额外的空间
一般在后处理效果（Post-processing）时使用
URP可以在管线资源或相机属性中设置深度图的开启/关闭
![Alt text](assets/unity_render_pipeline/image-4.png)
![Alt text](assets/unity_render_pipeline/image-5.png)

### Opaque Texture（截屏快照）
截屏快照是在渲染透明物体前（不透明物体已渲染），将当前场景渲染在一张图中保存
可以用截屏快照来实现一些效果，如水面折射，热浪等
创建截屏快照会占用额外的空间
URP可以在管线资源或相机属性中设置截屏快照的开启/关闭
![Alt text](assets/unity_render_pipeline/image-6.png)
![Alt text](assets/unity_render_pipeline/image-7.png)
#### Opaque Downsampling（截屏快照采样模式）
None 快照分辨率和相机分辨率等同
2x Bilieaner 快照分辨率为相机分辨率的1/2（线性滤波）
4x Box 快照分辨率为相机分辨率的1/4（方框滤波）
4x Bilieaner 快照分辨率为相机分辨率的1/4（线性滤波）
#### Terrain Holes（地形孔洞）

#### SRP Batcher（SRP合批）
可以对使用相同着色器的不同材质进行合批，减少渲染时CPU消耗时间


### Dynamic Range（动态范围）
亮度显示的范围
#### SDR（Standard Dynamic Range 低动态范围）
RGB单个通道值为8bit大小，也就是0~255
#### HDR（High Dynamic Range 高动态范围）
RGB单个通道值范围大于8bit，存储类型为float
HDR通过更广阔的取值范围可以更好的反映现实世界的颜色，更好地显示效果
在管线或相机中设置HDR
![Alt text](assets/unity_render_pipeline/image-1.png)
![Alt text](assets/unity_render_pipeline/image-2.png)
##### HDR Rendering
将场景（Scene）内容渲染到HDR图，并执行渲染操作（例如后处理效果）
##### HDR Output
将渲染后的HDR目标内容呈现在显示设备上（需要硬件支持）
##### Tonemapping
当设备只支持显示SDR时，将HDR内容转换成SDR，以便在设备上显示

## 渲染顺序
[Unity 渲染顺序（Rendering Order）](https://zhuanlan.zhihu.com/p/473875401)
1.Camera Depth（相机深度）
值越小越先被渲染
2.Render Queue（材质渲染队列）
分为2类 不透明和透明 <=2500被视为不透明物体优先于>2500被视为透明物体渲染
3.Sorting Layer（排列层级）
根据Sorting Layer设置的顺序来设定优先级 没有设置的话则默认为Default
![Alt text](assets/unity_render_pipeline/image-8.png)
4.Order in Layer（同Sorting Layer层级排序）
没有设置默认为0 值越小越优先
Sorting Layer和Order in Layer可以通过组件Sorting Group进行设置
![Alt text](assets/unity_render_pipeline/image-9.png)
5.Render Queue（材质渲染队列）
值越小越优先
6.Z Distance（物体距离相机距离）
不透明物体优先从近到远 透明物体优先从远到近

## 渲染优化
### 批处理
[关于静态批处理/动态批处理/GPU Instancing /SRP Batcher的详细剖析](https://zhuanlan.zhihu.com/p/98642798)

批处理，是一种在CPU应用阶段，将**相同渲染状态**的物体合并提交的一种优化手段，主要减少了CPU频繁切换渲染状态所造成的消耗。是一种当CPU负载高于GPU时可以使用的优化方式

#### 静态合批（static batch）

##### 定义
在Build（项目构建）时，会提取场景内所有静态物体的Vertex Buffer(顶点缓冲)和Index Buffer(索引缓冲)，将这些物体的顶点数据转化到世界空间下，最终生成2份所有顶点的Vertex Buffer和Index Buffer，并记录单个物体Index Buffer在这份巨大的Index Buffer对应的起始和结束位置
在实际渲染中，会将这份合并了所有顶点的数据加载到内存并上传到显存，设置渲染状态，最后发送Draw Call（1个静态物体1个）执行渲染（每个物体实际的顶点数据根据Index Buffer记录获取）
##### 优点
减少切换渲染状态频率
##### 缺点
生成的Vertex Buffer会包体增大
生成的Vertex Buffer会内存占用增加（物体太多可能会导致严重的内存问题）
合批后，静态物体的Transform不能改变
##### 合批限制
必须使用相同材质
##### 开启方式
旋转物体属性面板右上角Static勾选
![alt text](assets/unity_render_pipeline/image-10.png)



## GPU
### Constant Buffer（CBUFFER 常量缓冲区）
一块预先分配好大小的高速显存，用于存放Shader中的常量数据（矩阵、向量等）
容量不大，但可以和GPU快速的交换数据