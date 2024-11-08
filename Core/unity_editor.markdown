- [Editor（编辑器）](#editor编辑器)
  - [InitializeOnLoad（类方法自动初始化）](#initializeonload类方法自动初始化)
  - [Cutsom Class Editor（自定义组件编辑）](#cutsom-class-editor自定义组件编辑)
    - [finishedDefaultHeaderGUI（Inspector面板版头区域绘制）](#finisheddefaultheaderguiinspector面板版头区域绘制)
      - [特定组件的GameObject](#特定组件的gameobject)
      - [全部GameObject](#全部gameobject)
    - [OnInspectorGUI（Inspector面板绘制）](#oninspectorguiinspector面板绘制)
    - [OnSceneGUI（Scene面板绘制）](#onsceneguiscene面板绘制)
      - [Handles](#handles)
        - [Handles.BeginGUI \&\& Handles.EndGUI（设置GUI区间）](#handlesbegingui--handlesendgui设置gui区间)
        - [HandleUtility.AddDefaultControl（启用Scene场景GUI交互）](#handleutilityadddefaultcontrol启用scene场景gui交互)
      - [Tools](#tools)
        - [Tools.current](#toolscurrent)
    - [2](#2)
  - [Editor GUI（UI绘制 可用在OnInspectorGUI/OnSceneGUI中）](#editor-guiui绘制-可用在oninspectorguionscenegui中)
    - [UI Class（绘制UI用到的几个核心类）](#ui-class绘制ui用到的几个核心类)
      - [GUI](#gui)
      - [EditorGUI](#editorgui)
      - [GUILayout](#guilayout)
      - [EditorGUILayout](#editorguilayout)
      - [GUIStyle](#guistyle)
      - [GUILayout](#guilayout-1)
    - [PropertyField（属性值）](#propertyfield属性值)
    - [Space（空间）](#space空间)
    - [Box（区块）](#box区块)
    - [Label（文本标签）](#label文本标签)
    - [Button（按钮）](#button按钮)
    - [Toggle（单选框）](#toggle单选框)
    - [Toggle Group（多选组）](#toggle-group多选组)
    - [ToolBar（单选组）](#toolbar单选组)
    - [Selection Grid（单选组）](#selection-grid单选组)
    - [TextField（文本输入框）](#textfield文本输入框)
    - [ColorField（颜色输入框）](#colorfield颜色输入框)
    - [ObjectField（物件输入框）](#objectfield物件输入框)
    - [Slider（滑动条）](#slider滑动条)
    - [ScrollView（滑动组）](#scrollview滑动组)
    - [Horizontal（横向排序）](#horizontal横向排序)
    - [Vertical（纵向排序）](#vertical纵向排序)
  - [Editor Event（交互相关）](#editor-event交互相关)
    - [Event（Editor下获取当前事件类型）](#eventeditor下获取当前事件类型)
    - [Selection（选中节点）](#selection选中节点)
      - [Selection.count（选中节点数量）](#selectioncount选中节点数量)
      - [Selection.activeObject（选中文件）](#selectionactiveobject选中文件)
      - [Selection.objects（选中的所有文件）](#selectionobjects选中的所有文件)
      - [Selection.activeGameObject（选中预制）](#selectionactivegameobject选中预制)
      - [Selection.gameObjects（选中的所有预制）](#selectiongameobjects选中的所有预制)
    - [DragAndDrop（拖放节点）](#draganddrop拖放节点)
      - [DragAndDrop.paths（当前拖拽的所有文件路径）](#draganddroppaths当前拖拽的所有文件路径)
      - [DragAndDrop.objectReferences（当前拖拽的所有物体信息）](#draganddropobjectreferences当前拖拽的所有物体信息)
      - [使用事例](#使用事例)
  - [Event（GUI交互（键盘/鼠标）事件）](#eventgui交互键盘鼠标事件)
    - [Event.current（当前交互）](#eventcurrent当前交互)
  - [PrefabUtility](#prefabutility)
    - [PrefabUtility.SavePrefabAsset(GameObject go)](#prefabutilitysaveprefabassetgameobject-go)


# Editor（编辑器）

## InitializeOnLoad（类方法自动初始化）
当UnityEditor启动时，会自动执行带有[InitializeOnLoad]类的**静态**构造方法
```C
[InitializeOnLoad]
public class MyEditorInitializer
{
    static MyEditorInitializer()
    {
        Debug.Log("Initialize Success");
    }
}
```
![alt text](assets/unity_editor/image-37.png)

## Cutsom Class Editor（自定义组件编辑）
声明自定义组件的编辑类，命名为组件类名+Editor（如自定义组件为CustomComponent1，则对应的编辑器名称为CustomComponent1Editor）
继承UnityEditor.Editor
![Alt text](assets/unity_editor/image.png)
添加CustomEditor特性
![Alt text](assets/unity_editor/image-1.png)

### finishedDefaultHeaderGUI（Inspector面板版头区域绘制）
可以通过注册Editor.finishedDefaultHeaderGUI来实现Inspector的版头区域的GUI绘制，该方法每帧都会调用
```C
void OnFinishedDefaultHeaderGUI(UnityEditor.Editor editor)
{
    var go = editor.target as GameObject;
    if (go == null)
    {
        return;
    }
        
    EditorGUILayout.LabelField("Custom Header GUI");
    GUILayout.Button("Custom Button");
}
```
![alt text](assets/unity_editor/image-39.png)

#### 特定组件的GameObject
在组件Editor的OnEnable注册事件
```C
private void OnEnable()
{
    UnityEditor.Editor.finishedDefaultHeaderGUI += OnFinishedDefaultHeaderGUI;
}
```
在OnDisable取消事件
```C
private void OnEnable()
{
    UnityEditor.Editor.finishedDefaultHeaderGUI -= OnFinishedDefaultHeaderGUI;
}
```
#### 全部GameObject
在持有[InitializeOnLoad]类的静态构造函数中，注册相关事件
```C
[InitializeOnLoad]
public class MyEditorInitializer
{
    static MyEditorInitializer()
    {
        Debug.Log("Initialize Success");
        
        UnityEditor.Editor.finishedDefaultHeaderGUI += OnFinishedDefaultHeaderGUI;
    }
}
```

### OnInspectorGUI（Inspector面板绘制）
在继承UnityEditor.Editor的脚本中，声明函数OnInspectorGUI
在函数内声明绘制UI方法
```C
public void OnInspectorGUI()
{      
  GUI.Box(new Rect(100, 0, 100, 100), "Box 1");
  GUILayout.Box("Box 2", GUILayout.Width(100), GUILayout.Height(100));
}
```
![alt text](assets/unity_editor/image-3.png)

### OnSceneGUI（Scene面板绘制）
在继承UnityEditor.Editor的脚本中，声明函数OnSceneGUI
要绘制UI需要在Handles.BeginGUI和Handles.EndGUI之间，因此需要先声明这两个方法，最后在方法区间声明绘制UI方法
```C
public void OnSceneGUI()
{
  Handles.BeginGUI();
            
  GUI.Box(new Rect(400, 0, 200, 200), "Box 1");
  GUILayout.Box("Box 2", GUILayout.Width(200), GUILayout.Height(200));

  Handles.EndGUI();
}
```
![alt text](assets/unity_editor/image-2.png)

#### Handles
Handles类主要用于绘制Scene面板
![alt text](assets/unity_editor/image-27.png)

##### Handles.BeginGUI && Handles.EndGUI（设置GUI区间）
在Scene内绘制GUI需要在这2个命令之间
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



### 2


## Editor GUI（UI绘制 可用在OnInspectorGUI/OnSceneGUI中）

### UI Class（绘制UI用到的几个核心类）
#### GUI 
用于绘制基本的用户界面元素，如按钮、文本框等。需要手动管理布局和位置

#### EditorGUI 
扩展了GUI类，提供了一些额外的编辑器特定控件和功能

#### GUILayout 
用于绘制基本的用户界面元素，如按钮、文本框等。提供了自动布局的功能，无需手动管理控件的位置和大小

#### EditorGUILayout 
扩展了GUILayout类，提供了一些额外的编辑器特定控件和功能

#### GUIStyle 
定义GUI样式（GUIStyle.fontSize/GUIStyle.normal.textColor等），可在上面创建GUI时作为参数传入

#### GUILayout 
定义GUI样式（GUILayout.Width/GUILayout.Height等）， 可在GUILayout/EditorGUILayout创建GUI时作为GUILayoutOption参数传入

自定义类
```C
public enum EFood
{
    Pork,
    Lamb,
}

[Serializable]
public class Food
{
    [SerializeField]
    private EFood m_Option;
    
    [SerializeField]
    private int m_Price;
    
    [Range(1, 10)] 
    public int num;
}

public class CustomComponent1 : MonoBehaviour
{
    [SerializeField]
    private Food m_Food;

    [SerializeField]
    private string m_Desc;
    
    [SerializeField]
    private int m_CustomNum;

    [SerializeField]
    private GameObject m_GameObject;
}
```

### PropertyField（属性值）
Unity根据当前属性类型自动绘制UI，属性类型为Class时，需要添加Serializable标签
```C
m_Food = serializedObject.FindProperty("m_Food");
EditorGUILayout.PropertyField(m_Food);

m_Desc = serializedObject.FindProperty("m_Desc");
EditorGUILayout.PropertyField(m_Desc);

m_CustomNum = serializedObject.FindProperty("m_CustomNum");
EditorGUILayout.PropertyField(m_CustomNum);

m_GameObject = serializedObject.FindProperty("m_GameObject");
EditorGUILayout.PropertyField(m_GameObject);

```
![alt text](assets/unity_editor/image-10.png)

### Space（空间）
增加UI之间的空间
```C
GUILayout.Space(100);
EditorGUILayout.Space(100);
```
![alt text](assets/unity_editor/image-11.png)

### Box（区块）
```C
GUI.Box(new Rect(0, 0, 100, 100), "Box 1");
GUILayout.Box("Box 2", GUILayout.Width(100), GUILayout.Height(100));
```
![alt text](assets/unity_editor/image-26.png)

### Label（文本标签）
```C
GUI.Label(new Rect(0, 0, 100, 30), "Label 1");
EditorGUI.LabelField(new Rect(0, 30, 100, 30), "Label 2");
GUILayout.Label("Label 3", GUILayout.Width(100), GUILayout.Height(50));
EditorGUILayout.LabelField("Label 4", GUILayout.Width(100),GUILayout.Height(30));
```
![alt text](assets/unity_editor/image-12.png)

### Button（按钮）
```C
m_Button = GUI.Button(new Rect(150, 0, 100, 30), "Button 1");
m_Button = EditorGUI.DropdownButton(new Rect(150, 50, 100, 50), new GUIContent("Button 2"), FocusType.Passive);
m_Button = GUILayout.Button("Button 3", GUILayout.Width(100), GUILayout.Height(30));
m_Button = EditorGUILayout.DropdownButton(new GUIContent("Button 4"), FocusType.Passive, GUILayout.Width(100), GUILayout.Height(30));
```
![alt text](assets/unity_editor/image-17.png)

### Toggle（单选框）
```C
m_Toggle = GUI.Toggle(new Rect(0, 0, 100, 30), m_Toggle, "Toggle 1");
m_Toggle = GUILayout.Toggle(m_Toggle, "Toggle 2", GUILayout.Width(70), GUILayout.Height(70));
m_Toggle = EditorGUILayout.Toggle("Toggle 3", m_Toggle);
m_Toggle = EditorGUILayout.ToggleLeft("Toggle 4", m_Toggle);
```
![alt text](assets/unity_editor/image-13.png)

### Toggle Group（多选组）
```C
using (var toggleGroup = new EditorGUILayout.ToggleGroupScope("Toggle Group", m_Toggle))
{
    m_Toggle = toggleGroup.enabled;
    m_Toggle1 = EditorGUILayout.Toggle("Toggle 1", m_Toggle1);
    m_Toggle2 = EditorGUILayout.Toggle("Toggle 2", m_Toggle2);
}
```
![alt text](assets/unity_editor/image-14.png)

### ToolBar（单选组）
排列一行
```C
var grids = new string[] {"Toggle 1", "Toggle 2", "Toggle 3", "Toggle 4"};
m_ToolBarIndex = GUILayout.Toolbar(m_ToolBarIndex, grids);
```
![alt text](assets/unity_editor/image-16.png)

### Selection Grid（单选组）
可设置排列多行
```C
var grids = new string[] {"Toggle 1", "Toggle 2", "Toggle 3", "Toggle 4"};
m_SelectionGrid = GUILayout.SelectionGrid(m_SelectionGrid, grids, 2, GUILayout.Width(200));
```
![alt text](assets/unity_editor/image-15.png)

### TextField（文本输入框）
```C
m_TextField = GUI.TextField(new Rect(200, 0, 200, 30), m_TextField);
m_TextField = EditorGUI.TextField(new Rect(200, 50, 300, 30),"TextField 1", m_TextField);
m_TextField = GUILayout.TextField(m_TextField, GUILayout.Width(200), GUILayout.Height(30));
m_TextField = EditorGUILayout.TextField("TextField 2", m_TextField, GUILayout.Width(300), GUILayout.Height(30));
```
![alt text](assets/unity_editor/image-18.png)

### ColorField（颜色输入框）
```C
m_Color = EditorGUI.ColorField(new Rect(200, 0, 200, 20), m_Color);
m_Color = EditorGUILayout.ColorField("ColorField 1", m_Color);
```
![alt text](assets/unity_editor/image-19.png)

### ObjectField（物件输入框）
```C
EditorGUI.ObjectField(new Rect(0, 0, 400, 20), m_GameObject);
m_Object = EditorGUILayout.ObjectField("ObjectField 1", m_Object, typeof(UnityEngine.Object));
```
![alt text](assets/unity_editor/image-20.png)

### Slider（滑动条）
```C
m_SliderValue = GUI.HorizontalSlider(new Rect(0, 0, 300, 20),m_SliderValue, 0, 99);
m_SliderValue = EditorGUI.Slider(new Rect(0, 30, 300, 20),m_SliderValue, 0, 99);
m_SliderValue = GUILayout.HorizontalSlider(m_SliderValue, 0, 99);
m_SliderValue = EditorGUILayout.Slider(m_SliderValue, 0, 99);
```
![alt text](assets/unity_editor/image-21.png)

### ScrollView（滑动组）
```C
m_ScrollPos = EditorGUILayout.BeginScrollView(m_ScrollPos, GUILayout.Width(100), GUILayout.Height(100));
for (int i = 0; i < 20; i++)
{
    EditorGUILayout.LabelField("Label " + i);
}
EditorGUILayout.EndScrollView();
```
滑动内容需要创建于BeginScrollView到EndScrollView区间，并且宽/高超出滑动组范围，则会自动显示水平/垂直滑动条并可拖动
![alt text](assets/unity_editor/image-22.png)

### Horizontal（横向排序）
使用GUILayout、EditorGUILayout创建的UI默认为从上到下的纵向排序，可以将创建的UI元素包裹在EditorGUILayout.BeginHorizontal和EditorGUILayout.EndHorizontal之间，则会变成从左到右的横向排序
```C
EditorGUILayout.BeginHorizontal();

EditorGUILayout.LabelField("Label 1");
EditorGUILayout.TextField("TextField 1");
GUILayout.Button("Button 1");

EditorGUILayout.EndHorizontal();
```
![alt text](assets/unity_editor/image-23.png)

```C
m_ScrollPos = EditorGUILayout.BeginScrollView(m_ScrollPos, GUILayout.Width(300), GUILayout.Height(100));
            
EditorGUILayout.BeginHorizontal();

for (int i = 0; i < 20; i++)
{
    EditorGUILayout.LabelField("Label " + i);
}

EditorGUILayout.EndHorizontal();

EditorGUILayout.EndScrollView();
```
也可以将其作用在滑动组内容上，将其变为横向滑动组
![alt text](assets/unity_editor/image-24.png)

### Vertical（纵向排序）
使用GUILayout、EditorGUILayout创建的UI默认就为纵向排序，因此一般是搭配横向排序来嵌套使用
```C
EditorGUILayout.BeginHorizontal();
            
EditorGUILayout.LabelField("Label 1");

EditorGUILayout.BeginVertical();

EditorGUILayout.LabelField("Label 2");

EditorGUILayout.LabelField("Label 3");

EditorGUILayout.EndVertical();

EditorGUILayout.EndHorizontal();
```
![alt text](assets/unity_editor/image-25.png)

## Editor Event（交互相关）

### Event（Editor下获取当前事件类型）

### Selection（选中节点）
可以通过Selection获取在Hierarchy和Project窗口中选中的节点信息

#### Selection.count（选中节点数量）
![alt text](assets/unity_editor/image-28.png)

#### Selection.activeObject（选中文件）
包含Hierarchy和Project里的所有文件类型，如果选中复数物体的话，获取到的则是其中之一的信息
![alt text](assets/unity_editor/image-29.png)

#### Selection.objects（选中的所有文件）
包含Hierarchy和Project里的所有文件类型，可以获取到所有选中物体的信息
![alt text](assets/unity_editor/image-30.png)

#### Selection.activeGameObject（选中预制）
包含Hierarchy展示的预制节点和Project里的预制体，如果选中复数预制的话，获取到的则是其中之一的信息（选中的如果是其他文件类型则activeGameObject=null）
![alt text](assets/unity_editor/image-31.png)

#### Selection.gameObjects（选中的所有预制）
包含Hierarchy展示的预制节点和Project里的预制体，可以获取到所有选中预制的信息（选中的如果是其他文件类型则对应的GameObject=null）
![alt text](assets/unity_editor/image-32.png)

### DragAndDrop（拖放节点）

#### DragAndDrop.paths（当前拖拽的所有文件路径）
包含Project里的所有文件类型，可以获取到所有拖拽文件的路径
![alt text](assets/unity_editor/image-33.png)

#### DragAndDrop.objectReferences（当前拖拽的所有物体信息）
包含Hierarchy的所有物体及Project里的所有文件类型，可以获取到所有拖拽物体的信息
![alt text](assets/unity_editor/image-34.png)

#### 使用事例
当物体被拖拽到特定区域时，打印物体信息
```C      
Rect dropArea = new Rect(10, 10, 200, 100);
GUI.Box(dropArea, "拖放区域");

Event evt = Event.current;
switch (evt.type)
{
    case EventType.DragUpdated:
        if (dropArea.Contains(evt.mousePosition))
        {
            DragAndDrop.visualMode = DragAndDropVisualMode.Copy;
            Event.current.Use();
        }
        break;

    case EventType.DragPerform:
        if (dropArea.Contains(evt.mousePosition))
        {
            DragAndDrop.AcceptDrag();
            foreach (Object obj in DragAndDrop.objectReferences)
            {
                Debug.Log("拖放的对象：" + obj.name);
            }
            Event.current.Use();
        }
        break;

    case EventType.DragExited:
        Debug.Log("拖放操作已取消或拖离目标区域");
        Event.current.Use();
        break;
}
```
![alt text](assets/unity_editor/image-36.png)


Editor.finishedDefaultHeaderGUI

EditorApplication.hierarchyWindowItemOnGUI

## Event（GUI交互（键盘/鼠标）事件）

### Event.current（当前交互）
![Alt text](assets/unity_editor/image-6.png)

## PrefabUtility
###  PrefabUtility.SavePrefabAsset(GameObject go)
在Editor模式下修改GameObject后，使用该命令将变动保存到硬盘





