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

## Render（渲染器）
### 作用
Mesh Render的基类
### 原理
### 使用
render.material 获取该渲染器持有的第一个材质，并创建一个新的材质实例并返回。对返回的材质进行属性修改不会影响到其他使用该材质的物件，但是当物件销毁时需要记得手动销毁该材质，否则会一直留存在内存中
render.sharedMaterial 获取该渲染器持有的材质，并返回该材质的共有实例。对返回的材质进行修改，会改动到所有使用该材质的物件，并且材质本身的文件也会受到修改