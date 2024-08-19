- [Unity UGUI](#unity-ugui)
  - [EventSystem（交互事件系统）](#eventsystem交互事件系统)
    - [流程](#流程)
      - [EventSystem每帧检测是否有交互事件响应](#eventsystem每帧检测是否有交互事件响应)
      - [有交互事件时，保存交互数据（比如点击位置）](#有交互事件时保存交互数据比如点击位置)
      - [根据交互数据（比如点击位置），筛选这次交互最终作用到的GameObject](#根据交互数据比如点击位置筛选这次交互最终作用到的gameobject)
        - [筛选出所有作用到的Canvas](#筛选出所有作用到的canvas)
          - [获取所有包含BaseRaycaster组件的GameObject](#获取所有包含baseraycaster组件的gameobject)
          - [依次对这些GameObject进行筛选](#依次对这些gameobject进行筛选)
        - [筛选出Canvas下所有作用到的包含Graphic组件的GameObject](#筛选出canvas下所有作用到的包含graphic组件的gameobject)
          - [条件检测](#条件检测)
          - [排序](#排序)
          - [二次检测](#二次检测)
          - [二次排序](#二次排序)
        - [排序后首个GameObject就是最终作用到的物体](#排序后首个gameobject就是最终作用到的物体)
      - [调用筛选出的GameObject实现的不同交互函数（如点击、拖拽）](#调用筛选出的gameobject实现的不同交互函数如点击拖拽)
        - [获取实现各个交互接口的GameObject](#获取实现各个交互接口的gameobject)
        - [调用实现方法](#调用实现方法)



# Unity UGUI

## EventSystem（交互事件系统）

### 流程

#### EventSystem每帧检测是否有交互事件响应
![alt text](assets/unity_ugui/image-54.png)

#### 有交互事件时，保存交互数据（比如点击位置）
![alt text](assets/unity_ugui/image-55.png)

#### 根据交互数据（比如点击位置），筛选这次交互最终作用到的GameObject
![alt text](assets/unity_ugui/image-57.png)

##### 筛选出所有作用到的Canvas

###### 获取所有包含BaseRaycaster组件的GameObject
按照存入RaycasterManager的顺序获取所有处于Active状态的GameObject
![alt text](assets/unity_ugui/image-58.png)
拥有继承BaseRaycaster组件的物体在创建时会记录到RaycasterManager（Unity中管理Raycaster的静态类），比如Canvas持有GraphicRaycaster，Camera持有的PhysicsRaycaster
![alt text](assets/unity_ugui/image-59.png)
![alt text](assets/unity_ugui/image-60.png)

###### 依次对这些GameObject进行筛选
![alt text](assets/unity_ugui/image-61.png)

不是Canvas则排除
![alt text](assets/unity_ugui/image-62.png)

Canvas下没有任何持有Graphic组件的GameObject则排除（继承Graphic组件（Image，Text）的物体，在Enable时会以根节点的Canvas为键值记录在GraphicRegistry里）
![alt text](assets/unity_ugui/image-63.png)

交互位置超出屏幕范围则排除
![alt text](assets/unity_ugui/image-64.png)

##### 筛选出Canvas下所有作用到的包含Graphic组件的GameObject

###### 条件检测
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

###### 排序
按照depth属性，从大到小进行排序
![alt text](assets/unity_ugui/image-75.png)

###### 二次检测
当ignoreReversedGraphics属性为true时，根据z轴判断GameObject面对相机朝向，不在相机前方排除
![alt text](assets/unity_ugui/image-76.png)
![alt text](assets/unity_ugui/image-77.png)

###### 二次排序

GameObject所属Canvas不属于同一个相机，并且相机depth属性不同，数值大的靠前
![alt text](assets/unity_ugui/image-78.png)

GameObject所属Canvas的sortOrderPriority属性不同，数值大的靠前
![alt text](assets/unity_ugui/image-79.png)

GameObject所属Canvas的renderOrderPriority属性不同，数值大的靠前
![alt text](assets/unity_ugui/image-80.png)

GameObject所属Canvas的sortingLayerID属性不同，数值大的靠前
![alt text](assets/unity_ugui/image-81.png)

GameObject所属Canvas的sortingOrder属性不同，数值大的靠前
![alt text](assets/unity_ugui/image-82.png)

GameObject所属Canvas都是同一个或者最上层根节点Canvas都是同一个，depth属性不同，数值大的靠前
![alt text](assets/unity_ugui/image-83.png)

GameObject距离所属相机的距离不同，距离相机近的靠前
![alt text](assets/unity_ugui/image-84.png)

GameObject加入队列的索引不同，索引较小的靠前
![alt text](assets/unity_ugui/image-85.png)

##### 排序后首个GameObject就是最终作用到的物体
![alt text](assets/unity_ugui/image-86.png)

#### 调用筛选出的GameObject实现的不同交互函数（如点击、拖拽）

##### 获取实现各个交互接口的GameObject
![alt text](assets/unity_ugui/image-87.png)

获取GameObject上的组件，依次判断是否有组件实现对应事件接口
![alt text](assets/unity_ugui/image-89.png)

有的话则用这个GameObject来实现该事件函数，没有的话则查找其父节点（再不行则以父节点依次向上）
![alt text](assets/unity_ugui/image-88.png)

##### 调用实现方法
对GameObject上所有实现对应事件接口的组件依次进行方法调用
![alt text](assets/unity_ugui/image-90.png)
![alt text](assets/unity_ugui/image-91.png)