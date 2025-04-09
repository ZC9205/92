- [EventSystem（交互事件系统）](#eventsystem交互事件系统)
  - [流程](#流程)
    - [EventSystem每帧检测是否有交互事件响应](#eventsystem每帧检测是否有交互事件响应)
    - [有交互事件时，保存交互数据（比如点击位置）](#有交互事件时保存交互数据比如点击位置)
    - [根据交互数据（比如点击位置），筛选这次交互最终作用到的GameObject](#根据交互数据比如点击位置筛选这次交互最终作用到的gameobject)
      - [首先获取所有包含BaseRaycaster组件的GameObject（Camera和Canvas）](#首先获取所有包含baseraycaster组件的gameobjectcamera和canvas)
      - [PhysicsRaycaster根节点筛选（Camera）](#physicsraycaster根节点筛选camera)
      - [GraphicRaycaster根节点筛选（Canvas）](#graphicraycaster根节点筛选canvas)
        - [条件检测](#条件检测)
        - [排序](#排序)
        - [二次检测](#二次检测)
        - [通过检测](#通过检测)
      - [RaycastResults排序](#raycastresults排序)
        - [排序后首个RaycastResult的GameObject就是最终作用到的物体](#排序后首个raycastresult的gameobject就是最终作用到的物体)
      - [调用筛选出的GameObject所实现的不同交互函数（如点击、拖拽）](#调用筛选出的gameobject所实现的不同交互函数如点击拖拽)
        - [获取实现各个交互接口的GameObject](#获取实现各个交互接口的gameobject)
        - [调用实现方法](#调用实现方法)
  - [核心脚本](#核心脚本)
    - [Input.cs](#inputcs)


# EventSystem（交互事件系统）

## 流程

### EventSystem每帧检测是否有交互事件响应
![alt text](assets/unity_ugui/image-54.png)

### 有交互事件时，保存交互数据（比如点击位置）
![alt text](assets/unity_ugui/image-55.png)

### 根据交互数据（比如点击位置），筛选这次交互最终作用到的GameObject
![alt text](assets/unity_ugui/image-57.png)

#### 首先获取所有包含BaseRaycaster组件的GameObject（Camera和Canvas）
按照存入RaycasterManager的顺序获取所有处于Active状态的GameObject
![alt text](assets/unity_ugui/image-58.png)
拥有继承BaseRaycaster组件的物体在创建时会记录到RaycasterManager（Unity中管理Raycaster的静态类），比如GraphicRaycaster(Canvas交互需要)，PhysicsRaycaster（Camera交互需要）
![alt text](assets/unity_ugui/image-59.png)
![alt text](assets/unity_ugui/image-60.png)
接着以这些GameObject为根节点，筛选可以进行交互的GameObjects
![alt text](assets/unity_ugui/image-61.png)

#### PhysicsRaycaster根节点筛选（Camera）

不是Camera则排除
![alt text](assets/unity_event_system/image-3.png)

点击位置不是该Camera所属Display窗口则排除
![alt text](assets/unity_event_system/image-4.png)

点击位置未包含在相机可视区域内则排除
![alt text](assets/unity_event_system/image-5.png)

raycast3DAll选项为false则排除
![alt text](assets/unity_event_system/image-6.png)

执行射线碰撞检测，获取射线触碰到的持有碰撞盒组件的GameObjects
![alt text](assets/unity_event_system/image-9.png)

GameObjects数量为0则排除，数量超过1则排序
![alt text](assets/unity_event_system/image-7.png)

最后将获取到持有碰撞盒的GameObject放入raycastResults
![alt text](assets/unity_event_system/image-11.png)

#### GraphicRaycaster根节点筛选（Canvas）

不是Canvas则排除
![alt text](assets/unity_ugui/image-62.png)

Canvas下没有任何持有Graphic组件的GameObject则排除（继承Graphic组件（Image，Text）的物体，在Enable时会以根节点的Canvas为键值记录在GraphicRegistry里）
![alt text](assets/unity_ugui/image-63.png)

交互位置超出屏幕范围则排除
![alt text](assets/unity_ugui/image-64.png)

筛选出Canvas下所有作用到的包含Graphic组件的GameObject

##### 条件检测
![alt text](assets/unity_ugui/image-66.png)

未选中raycastTarget属性排除
![alt text](assets/unity_ugui/image-67.png)

CanvasRenderer组件选中cull属性排除
![alt text](assets/unity_ugui/image-68.png)

depth属性等于-1排除
![alt text](assets/unity_ugui/image-69.png)

屏幕触碰范围未包含在RectTransform范围内排除
![alt text](assets/unity_ugui/image-70.png)

位于相机的ClippingPlanes范围外排除
![alt text](assets/unity_ugui/image-71.png)

InActive或者Graphic组件Disabled排除
![alt text](assets/unity_ugui/image-72.png)

如果包含继承ICanvasRaycastFilter的组件（如CanvasGroup），IsRaycastLocationValid()结果为false排除
![alt text](assets/unity_ugui/image-73.png)

最终通过所有检测的Graphic加入列表
![alt text](assets/unity_ugui/image-74.png)

##### 排序
按照depth属性，从大到小进行排序
![alt text](assets/unity_ugui/image-75.png)

##### 二次检测
当ignoreReversedGraphics属性为true时，根据z轴判断GameObject面对相机朝向，不在相机前方排除
![alt text](assets/unity_ugui/image-76.png)
![alt text](assets/unity_ugui/image-77.png)

##### 通过检测
最后将通过所有检测的GameObject放入raycastResults
![alt text](assets/unity_event_system/image.png)

#### RaycastResults排序
对所有可进行交互的raycastResult进行排序，以此获取最终执行交互的GameObject
![alt text](assets/unity_event_system/image-1.png)

RaycastResult所属Module（Canvas/Camera）不同，渲染相机也不同，并且相机depth深度不同，数值大的靠前
![alt text](assets/unity_ugui/image-78.png)

RaycastResult所属Module的sortOrderPriority属性不同，数值大的靠前
![alt text](assets/unity_ugui/image-79.png)

RaycastResult所属Module的renderOrderPriority属性不同，数值大的靠前
![alt text](assets/unity_ugui/image-80.png)

RaycastResult的sortingLayerID属性不同，数值大的靠前
![alt text](assets/unity_ugui/image-81.png)

RaycastResult的sortingOrder属性不同，数值大的靠前
![alt text](assets/unity_ugui/image-82.png)

RaycastResult所属Module都是同一个或者最上层根节点都是同一个，depth属性不同，数值大的靠前
![alt text](assets/unity_ugui/image-83.png)

RaycastResult距离所属相机的距离不同，距离相机近的靠前
![alt text](assets/unity_ugui/image-84.png)

RaycastResult加入队列的索引不同，索引较小的靠前
![alt text](assets/unity_ugui/image-85.png)

##### 排序后首个RaycastResult的GameObject就是最终作用到的物体
![alt text](assets/unity_ugui/image-86.png)

#### 调用筛选出的GameObject所实现的不同交互函数（如点击、拖拽）

##### 获取实现各个交互接口的GameObject
首先获取刚筛选出的GameObject上的所有组件(Components)，判断是否有组件实现了对应事件的实现函数(就是组件是否继承了对应交互事件的接口，比如Button组件继承了点击事件的接口IPointerClickHandler)
![alt text](assets/unity_ugui/image-87.png)
![alt text](assets/unity_ugui/image-89.png)
![alt text](assets/unity_event_system/image-12.png)
![alt text](assets/unity_event_system/image-13.png)

有的话则保存这个GameObject作为实现该事件函数的对象，没有的话则查找其父节点是否满足实现（再不行则再以父节点依次向上）
![alt text](assets/unity_ugui/image-88.png)

##### 调用实现方法
根据刚才的保存结果，取出实现各个交互事件的GameObject，找到实现对应交互事件的函数，之后调用
![alt text](assets/unity_ugui/image-90.png)
![alt text](assets/unity_ugui/image-91.png)

## 核心脚本

### Input.cs
当前帧设备操作数据，包含操作设备（鼠标，键盘按键），操作行为（按下，松手），操作位置等等
EventSystem的逻辑（点击，拖拽）都是基于Input中获取的操作信息
因此有需要也可以根据Input数据自己实现一套物体交互逻辑