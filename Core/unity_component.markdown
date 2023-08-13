# Component(组件)
## Mask(遮罩)
### 作用
限制Mask组件Go下所有子节点的显示，超出Go所在Rect范围部分都不会显示
### 原理
Mask会修改默认的UI/Default材质，修改其名称并添加stencil(模板缓冲 不满足模板测试的片元会被丢弃，也就不显示)数值(所在位置片元_Stencil为1)
![Alt text](assets/unity_component/image-1.png)
Mask子节点会判定，如果其父节点包含了Mask，并且自己开启了Maskable勾选，也会修改默认的UI/Default材质，修改其名称并添加stencil数值(判断所在片元_Stencil值是否为1，1的话通过测试并渲染，不为1则舍弃)
![Alt text](assets/unity_component/image-2.png)
![Alt text](assets/unity_component/image-3.png)
