
# Optimization（性能分析）
## UGUI
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