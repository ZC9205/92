
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