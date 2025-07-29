- [Component(组件)](#component组件)
  - [Transform](#transform)
    - [Rotation（旋转方向）](#rotation旋转方向)
  - [Mask(遮罩)](#mask遮罩)
    - [作用](#作用)
    - [原理](#原理)
  - [Sorting Group](#sorting-group)
    - [作用](#作用-1)
  - [Render（渲染器）](#render渲染器)
    - [作用](#作用-2)
    - [原理](#原理-1)
    - [使用](#使用)

# Component(组件)


## Transform

### Rotation（旋转方向）
面板上的x、y、z，对应的是世界空间下的x、y、z轴，数值变动意为物体环绕着世界空间对应轴旋转该数值的角度
如下图x=20、y=20意为物体绕着世界坐标x轴旋转20°，又绕着世界坐标y轴旋转了20°
![alt text](assets/unity_component/image-4.png)
通过代码修改
![alt text](assets/unity_component/image-5.png)
transform.right/transform.up/transform.forward
意为物体旋转自身坐标轴的x/y/z轴，指向世界空间的某个坐标
如下图，transform.right=new Vector3(10,10,0)，意为旋转自身，让自身的x轴指向世界空间(10,10,0)的位置
![alt text](assets/unity_component/image-6.png)
![alt text](assets/unity_component/image-8.png)
![alt text](assets/unity_component/image-9.png)

## Mask(遮罩)
### 作用
限制Mask组件Go下所有子节点的显示，超出Go所在Rect范围部分都不会显示
### 原理
Mask会修改默认的UI/Default材质，修改其名称并添加stencil(模板缓冲 不满足模板测试的片元会被丢弃，也就不显示)数值(所在位置片元_Stencil为1)
![Alt text](assets/unity_component/image-1.png)
Mask子节点会判定，如果其父节点包含了Mask，并且自己开启了Maskable勾选，也会修改默认的UI/Default材质，修改其名称并添加stencil数值(判断所在片元_Stencil值是否为1，1的话通过测试并渲染，不为1则舍弃)
![Alt text](assets/unity_component/image-2.png)
![Alt text](assets/unity_component/image-3.png)

## Sorting Group
![alt text](assets/unity_component/image-10.png)
### 作用
Sorting Group可以设置该GameObject及其子节点的Sorting Layer和Order in Layer，影响渲染顺序
相机渲染顺序优先相机深度、不透明/透明，之后便根据Sorting Layer、Order in Layer来排序
如果没持有该组件，GameObject的默认Sorting Layer和Order in Layer都是0
**注意！Sorting Group设置优先级是越上级越优先，如果父节点和子节点同时持有Sorting Group组件，那么子节点本身的Sorting Group组件是不会生效的！子节点的Sorting Layer和Order in Layer是父节点Sorting Group设置的值。这个对Partical System（粒子特效）也是同样的，父节点如果有Sorting Group组件，那么它的值也会将Partical System中Renderer里的Sorting Layer、Order in Layer值覆盖**


## Render（渲染器）
### 作用
Mesh Render的基类
### 原理
### 使用
render.material 获取该渲染器持有的第一个材质，并创建一个新的材质实例并返回。对返回的材质进行属性修改不会影响到其他使用该材质的物件，但是当物件销毁时需要记得手动销毁该材质，否则会一直留存在内存中
render.sharedMaterial 获取该渲染器持有的材质，并返回该材质的共有实例。对返回的材质进行修改，会改动到所有使用该材质的物件，并且材质本身的文件也会受到修改