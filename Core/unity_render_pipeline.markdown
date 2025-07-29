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
    - [Set Pass Call](#set-pass-call)
    - [Batch](#batch)
    - [DrawCall](#drawcall)
    - [批处理](#批处理)
      - [UGUI合批（UGUI batching）](#ugui合批ugui-batching)
        - [定义](#定义)
          - [UGUI元素定义](#ugui元素定义)
          - [UGUI元素排序](#ugui元素排序)
          - [UGUI元素合批与渲染](#ugui元素合批与渲染)
          - [其他注意事项](#其他注意事项)
        - [合批限制](#合批限制)
      - [静态合批（static batching）](#静态合批static-batching)
        - [定义](#定义-1)
        - [优点](#优点)
        - [缺点](#缺点)
        - [合批限制](#合批限制-1)
        - [开启方式](#开启方式)
      - [动态合批（dynamic batching）](#动态合批dynamic-batching)
        - [定义](#定义-2)
        - [优点](#优点-1)
        - [缺点](#缺点-1)
        - [合批限制](#合批限制-2)
        - [注意](#注意)
        - [开启方式](#开启方式-1)
      - [GPU Instancing](#gpu-instancing)
        - [定义](#定义-3)
        - [优点](#优点-2)
        - [缺点](#缺点-2)
        - [合批限制](#合批限制-3)
        - [开启方式](#开启方式-2)
      - [SRP Batcher](#srp-batcher)
        - [定义](#定义-4)
        - [优点](#优点-3)
        - [缺点](#缺点-3)
        - [合批限制](#合批限制-4)
        - [注意](#注意-1)
        - [开启方式](#开启方式-3)
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
特殊情况：
UGUI元素（Canvas下的可视元素），每个Canvas会对自己内部的UGUI元素进行排序并合并批次，之后再将合并后的一个批次作为一个可视物体，并和其他物体一起（如3D模型）参与到上面的渲染排序

## 渲染优化

### Set Pass Call
渲染时切换渲染状态的次数，也可以理解为每帧执行渲染的Shader Pass次数
Shader里的每段Pass都代表了一次完整的渲染过程（从顶点着色到颜色输出）
设置渲染状态是在CPU上进行的，会消耗较多的时间，因此减少Set Pass Call是优化性能的重要指标

### Batch
将满足合批条件物体的数据合并后每帧提交渲染的次数。需要注意的是有些合批方式并不会减少该次数，比如SRP Batch执行时虽然会减少渲染状态切换（Set Pass Call）及绑定物体数据的时间，但依然是每个物体单独渲染，因此不会减少Batch次数
Batch是性能优化的重要指标，理论上Batch越少性能越好（数据合并后一次性提交会比每条数据单独提交快很多），一个Batch至少包含了一条DrawCall

### DrawCall
CPU让GPU执行渲染的命令。实际只是一条包含极少数据的指令，并没有什么消耗

### 批处理
[关于静态批处理/动态批处理/GPU Instancing /SRP Batcher的详细剖析](https://zhuanlan.zhihu.com/p/98642798)
批处理，是一种在CPU应用阶段，将**相同渲染状态**的物体合并提交的一种优化手段，主要减少了CPU频繁切换渲染状态所造成的消耗。是一种当CPU负载高于GPU时可以使用的优化方式

#### UGUI合批（UGUI batching）
[Unity3D UGUI系列之合批](https://blog.csdn.net/sinat_25415095/article/details/112388638)
##### 定义
UGUI合批是将Canvas上满足合批条件的UGUI进行合并（顶点数据合并，网格合并），形成一个新物体后，再加入渲染流程
###### UGUI元素定义
UGUI元素为需要放置在Canvas（画布）上才能进行渲染的元素，需要继承ICanvasElement接口
###### UGUI元素排序
每个Canvas会对其节点下所有UGUI元素进行排序（排除嵌套Canvas，嵌套Canvas下的元素归属于嵌套Canvas本身）
1.获取Canvas下UGUI元素的depth（深度值），按照depth从小到大排序，depth相同则参考material ID
depth（深度值），Canvas下可显示的UGUI元素都有其depth。不显示的depth=-1，最底层的显示元素depth=0，层级往上叠加且不满足合批条件则depth+1（比如depth=1元素需要满足3个条件 1.Hierarchy的位置在depth=0元素之后 2.有部分网格和depth=0元素的网格重叠（Rotation、Scale、Pos Z等属性调整会影响网格重叠判定） 3.和depth=0元素的材质或贴图不同）
2.按照material ID（材质id）从小到大排序，material ID相同则参考texture ID
3.按照texture ID（纹理id）从小到大排序，texture ID相同则参考Hierarchy上节点顺序
4.按照Hierarchy窗口Canvas节点往下从前到后排序
###### UGUI元素合批与渲染
当Canvas下所有元素排序后，从第一个元素开始判断是否满足和下一个元素合批的条件（相同的材质与贴图），可以的话则继续向后进行判断，直到某个元素不满足条件而中断。将前面所有满足条件的元素合并为一个新的物体（顶点数据合并，网格合并），接着继续这个流程，直到将Canvas下所有元素划分成若干个新的物体
合并后的新物体（Sorting Layer/Order in Layer/Z Distance这些属性和所属Canvas一致）会像其他可视物体（如3D模型）一起参与渲染排序，之后再进行渲染
###### 其他注意事项
当Canvas的Render Mode设置为Overlay，其节点下的所有UGUI会被Unity的隐藏相机（不会显示在Hierarchy节点内）渲染，由于隐藏相机的深度是最高的，所以UGUI会最后才渲染，也就是显示在最上层
当Canvas的Render Mode设置为Camera，其节点下的所有UGUI只会被Canvas设置的Render Camera渲染。如果没有设置Render Camera则将其Render Mode视为Overlay， 如果Canvas自身的Layer不包含在Camera可渲染的Layer之内（Culling Mask），则Canvas下的所有UGUI都不会被渲染
Canvas节点下UGUI的Layer实际上是跟随Canvas自身的Layer，因此设置UGUI的Layer本身并没有意义


##### 合批限制
必须使用相同材质
必须使用相同贴图

#### 静态合批（static batching）
[static-batching](https://docs.unity3d.com/Manual/static-batching.html)
[游戏图形批量渲染及优化：Unity静态合批技术](https://www.gameres.com/876479.html)
##### 定义
在Build（项目构建）时，会将场景内的所有静态物体网格顶点转化到世界空间下（为了让合并网格下的所有顶点坐标空间保持一致）并合并，最终生成包含所有顶点的Vertex Buffer和Index Buffer文件，并记录单个物体Index Buffer在这份巨大的Index Buffer对应的起始和结束位置
在实际渲染中，会将这份合并网格数据加载到内存，并在一个批次内绘制所有可见的静态物体，如果其中某些物体不可见（被剔除或者隐藏），会根据Index Buffer里的记录跳过对应物体网格，分化为多个Draw Call提交，但还是属于同个批次，并不会改变渲染状态
##### 优点
减少切换渲染状态频率
##### 缺点
生成的合并网格数据会使包体增大
生成的合并网格数据会常驻使内存占用增加（顶点数据太多可能会导致严重的内存问题）
##### 合批限制
必须使用相同材质
单批次上限为64k vertices
合批后，静态物体的Transform不能改变
##### 开启方式
旋转物体属性面板右上角Static勾选
![alt text](assets/unity_render_pipeline/image-10.png)

#### 动态合批（dynamic batching）
[dynamic-batching](https://docs.unity3d.com/Manual/dynamic-batching.html)
##### 定义
在渲染时，会根据渲染顺序查找前后符合动态合批条件的物体，将顶点转化到世界坐标空间后合并网格，并在一个批次内提交
##### 优点
方便，只要开启动态合批Unity就会自动执行合批
##### 缺点
限制较多，很多情况下无法合批
转换顶点坐标空间时本身会有一定消耗
##### 合批限制
必须使用相同材质
合批物体顶点不能超过300个（如果使用了更多着色器属性如UV0,UV1 顶点数量不能超过180个）
##### 注意
对于Shader变体（相同Shader不同材质），动态合批是无法进行的，会打断合批并重新设置渲染状态
##### 开启方式
内置渲染管线
![alt text](assets/unity_render_pipeline/image-11.png)
URP
![alt text](assets/unity_render_pipeline/image-12.png)
SRP
![alt text](assets/unity_render_pipeline/image-13.png)

#### GPU Instancing
[gpu-instancing](https://docs.unity3d.com/Manual/GPUInstancing.html)
##### 定义
这种合批方式只能作用于相同网格和材质的物体，一般作用于场景内大量相同重复物体如很多树
渲染时会只保存一份网格数据（因为物体网格都相同），分别保存每个物体的transform数据（位置 缩放 旋转），每个物体拥有一个独立的Instance_Id，最终将这些物体放在同一个Draw Call内渲染
可以使用MaterialPropertyBlock来修改个别物体的材质属性（包括纹理），不会影响到合批
是否成功合批可以在Frame Debugger中查看
![alt text](assets/unity_render_pipeline/image-27.png)

##### 优点
大批量相同物体渲染时优于静态合批（只需要保存一份网格数据）

##### 缺点
只限于网格相同物体

##### 合批限制
必须使用相同网格
必须使用相同材质
与SRP Batcher冲突（SRP Batcher优先级更高）

##### 开启方式
SRP开关
![alt text](assets/unity_render_pipeline/image-25.png)
在shader内设置开启
![alt text](assets/unity_render_pipeline/image-14.png)
Material面板属性开启
![alt text](assets/unity_render_pipeline/image-28.png)
定义Property里的属性值需要包裹在UNITY_INSTANCING_BUFFER_START和UNITY_INSTANCING_BUFFER_END（纹理属性除外）
![alt text](assets/unity_render_pipeline/image-15.png)
在顶点和片元着色器的输入中，加入Instance_Id
![alt text](assets/unity_render_pipeline/image-16.png)
![alt text](assets/unity_render_pipeline/image-17.png)
获取属性值通过UNITY_ACCESS_INSTANCED_PROP获取
![alt text](assets/unity_render_pipeline/image-18.png)

#### SRP Batcher
[srp-batcher](https://docs.unity3d.com/Manual/SRPBatcher.html)
##### 定义
可以将共享同Shader变体但材质不同的物体进行合批
使用专用代码将不同材质（相同Shader变体）的属性分别上传到GPU常量缓冲（Const Buffer）中，并持久保存
使用专用代码将物体相关属性（如transform）上传到GPU
不同材质的纹理图不同也不影响合批
满足SRP Batcher的shader面板选项会显示compatible
![alt text](assets/unity_render_pipeline/image-23.png)
是否成功合批可以在Frame Debugger中查看
![alt text](assets/unity_render_pipeline/image-24.png)

##### 优点
可以合批相同shader变体的不同材质，合批条件较宽
使用专用代码上传物体数据和材质属性，效率更高
上传的材质属性会持久保存在GPU的常量缓冲中，因此渲染同材质的物体时，不需要再次上传数据

##### 缺点


##### 合批限制
共享相同的着色器变体
不能是粒子
不能使用MaterialPropertyBlock来修改材质属性

##### 注意
SRP Batch合批方式并不像其他合批方式一样会合并满足合批条件的物体网格，实际渲染时还是依次绑定每个物体属性并单独渲染，因此并不会减少Batch和DrawCall

##### 开启方式
URP
![alt text](assets/unity_render_pipeline/image-22.png)
SRP
![alt text](assets/unity_render_pipeline/image-21.png)
将Property相关属性放置在UnityPerMaterial，相关内置引擎属性放置在UnityPerDraw
![alt text](assets/unity_render_pipeline/image-19.png)
![alt text](assets/unity_render_pipeline/image-20.png)


## GPU
### Constant Buffer（CBUFFER 常量缓冲区）
一块预先分配好大小的高速显存，用于存放Shader中的常量数据（矩阵、向量等）
容量不大，但可以和GPU快速的交换数据