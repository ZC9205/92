- [Editor（编辑器）](#editor编辑器)
  - [Cutsom Script Editor（自定义脚本编辑）](#cutsom-script-editor自定义脚本编辑)
    - [OnInspectorGUI（Inspector面板绘制）](#oninspectorguiinspector面板绘制)
    - [OnSceneGUI（Scene面板绘制）](#onsceneguiscene面板绘制)
      - [Handles](#handles)
        - [Handles.BeginGUI \&\& Handles.EndGUI（绘制GUI）](#handlesbegingui--handlesendgui绘制gui)
        - [HandleUtility.AddDefaultControl（启用Scene场景GUI交互）](#handleutilityadddefaultcontrol启用scene场景gui交互)
      - [Tools](#tools)
        - [Tools.current](#toolscurrent)
  - [Editor GUI（UI绘制）](#editor-guiui绘制)
    - [Label（标签）](#label标签)
    - [Toggle（勾选）](#toggle勾选)
    - [Slider（滑动条）](#slider滑动条)
    - [Button（按钮）](#button按钮)
    - [SelectionGrid（多选项）](#selectiongrid多选项)
    - [TextField（文本输入框）](#textfield文本输入框)
    - [ColorField（颜色输入框）](#colorfield颜色输入框)
    - [ObjectField（物件输入框）](#objectfield物件输入框)
    - [Scroll（滑动组件）](#scroll滑动组件)
    - [Space（空格）](#space空格)
    - [Horizontal（横向排序）](#horizontal横向排序)
    - [Vertical（纵向排序）](#vertical纵向排序)
    - [GUILayoutOption（GUI绘制参数（长、宽、样式））](#guilayoutoptiongui绘制参数长宽样式)
    - [GUIStyle（GUI模式类型）](#guistylegui模式类型)
  - [Event（GUI交互（键盘/鼠标）事件）](#eventgui交互键盘鼠标事件)
    - [Event.current（当前交互）](#eventcurrent当前交互)


# Editor（编辑器）

## Cutsom Script Editor（自定义脚本编辑）
继承UnityEditor.Editor
![Alt text](assets/unity_editor/image.png)
添加CustomEditor特性
![Alt text](assets/unity_editor/image-1.png)

### OnInspectorGUI（Inspector面板绘制）
重写OnInspectorGUI函数
![Alt text](assets/unity_editor/image-2.png)


### OnSceneGUI（Scene面板绘制）
声明OnSceneGUI函数
![Alt text](assets/unity_editor/image-3.png)

#### Handles
用于控制Scene场景里的物体

##### Handles.BeginGUI && Handles.EndGUI（绘制GUI）
在Scene内绘制GUI需要用这2个Handles命令包裹
![Alt text](assets/unity_editor/image-4.png)

##### HandleUtility.AddDefaultControl（启用Scene场景GUI交互）
GUIUtility.GetControlID(FocusType.Passive) 获取当前控制id
HandleUtility.AddDefaultControl(controlId) 设置id为默认控制
![Alt text](assets/unity_editor/image-5.png)

#### Tools
Scene视图上方的工具列表
![Alt text](assets/unity_editor/image-7.png)
![Alt text](assets/unity_editor/image-8.png)
##### Tools.current
当前选中的工具
![Alt text](assets/unity_editor/image-9.png)

## Editor GUI（UI绘制）

### Label（标签）
```C
GUI.Label(new Rect(0, 0, 100, 30), "标签1");
GUILayout.Label("标签2", GUILayout.Width(100), GUILayout.Height(30));
```
![Alt text](assets/unity_editor/image-10.png)

### Toggle（勾选）
```C
m_Toggle = GUI.Toggle(new Rect(0, 0, 100, 30), m_Toggle, "勾选1");
m_Toggle = GUILayout.Toggle(m_Toggle, "勾选2", GUILayout.Width(50), GUILayout.Height(30));
m_Toggle = EditorGUILayout.Toggle("勾选3", m_Toggle);
m_Toggle = EditorGUILayout.ToggleLeft("勾选4", m_Toggle);
```
![Alt text](assets/unity_editor/image-19.png)

### Slider（滑动条）
```C
m_SliderValue = EditorGUILayout.Slider("滑动条", m_SliderValue, 0, 10, GUILayout.Width(300));
```
![Alt text](assets/unity_editor/image-13.png)

### Button（按钮）
```C
m_Button = GUI.Button(new Rect(0, 0, 50, 30), "按钮1");
m_Button = GUILayout.Button("按钮2", GUILayout.Width(100), GUILayout.Height(30));
```
![Alt text](assets/unity_editor/image-14.png)

### SelectionGrid（多选项）
```C
var grids = new string[] {"一", "二", "三", "四", "五"};
m_SelectionGrid = GUILayout.SelectionGrid(m_SelectionGrid, grids, 3, GUILayout.Width(300));
```
![Alt text](assets/unity_editor/image-15.png)

### TextField（文本输入框）
```C
m_TextField = GUI.TextField(new Rect(0, 0, 50, 20), m_TextField);
m_TextField = GUILayout.TextField(m_TextField, GUILayout.Width(300));
m_TextField = EditorGUILayout.TextField("输入框", m_TextField, GUILayout.Width(300));
```
![Alt text](assets/unity_editor/image-16.png)

### ColorField（颜色输入框）
```C
m_Color = EditorGUILayout.ColorField("颜色", m_Color, GUILayout.Width(200), GUILayout.Height(20));
```
![Alt text](assets/unity_editor/image-17.png)

### ObjectField（物件输入框）
```C
m_Object = EditorGUILayout.ObjectField("物件", m_Object, typeof(UnityEngine.Object), GUILayout.Width(300));
```
![Alt text](assets/unity_editor/image-18.png)

### Scroll（滑动组件）
纵向滑动
```C
m_ScrollPos = EditorGUILayout.BeginScrollView(m_ScrollPos, GUILayout.Width(300), GUILayout.Height(400));
GUILayout.Button("按钮1", GUILayout.Width(200), GUILayout.Height(220));
GUILayout.Button("按钮2", GUILayout.Width(200), GUILayout.Height(220));
EditorGUILayout.EndScrollView();
```
![Alt text](assets/unity_editor/image-23.png)
横向滑动
```C
m_ScrollPos = EditorGUILayout.BeginScrollView(m_ScrollPos, GUILayout.Width(300), GUILayout.Height(400));
EditorGUILayout.BeginHorizontal();
GUILayout.Button("按钮1", GUILayout.Width(220), GUILayout.Height(220));
GUILayout.Button("按钮2", GUILayout.Width(220), GUILayout.Height(220));
EditorGUILayout.EndHorizontal();
EditorGUILayout.EndScrollView();
```
![Alt text](assets/unity_editor/image-24.png)
BeginScrollView到EndScrollView区间，内部UI超出滑动组件范围（水平或垂直），则会自动显示滑动条并可拖动

### Space（空格）
```C
GUILayout.Button("按钮1", GUILayout.Width(100), GUILayout.Height(100));
EditorGUILayout.Space(100);
GUILayout.Button("按钮2", GUILayout.Width(100), GUILayout.Height(100));
```
![Alt text](assets/unity_editor/image-25.png)

### Horizontal（横向排序）
```C
EditorGUILayout.BeginHorizontal();
GUILayout.Label("标签", GUILayout.Width(100));
EditorGUILayout.ToggleLeft("开关", false);
EditorGUILayout.TextField("输入框", "输入", GUILayout.Width(300));
EditorGUILayout.EndHorizontal();
```
![Alt text](assets/unity_editor/image-20.png)

### Vertical（纵向排序）
```C
EditorGUILayout.BeginVertical();
GUILayout.Label("标签", GUILayout.Width(100));
EditorGUILayout.ToggleLeft("开关", false);
EditorGUILayout.TextField("输入框", "输入", GUILayout.Width(300));
EditorGUILayout.EndVertical();
```
![Alt text](assets/unity_editor/image-21.png)

### GUILayoutOption（GUI绘制参数（长、宽、样式））
GUILayout.Width(10) 宽度为10
GUILayout.Height(10) 长度为10

### GUIStyle（GUI模式类型）
GUI.skin.label 标签类型
GUI.skin.button 按钮类型

## Event（GUI交互（键盘/鼠标）事件）

### Event.current（当前交互）
![Alt text](assets/unity_editor/image-6.png)