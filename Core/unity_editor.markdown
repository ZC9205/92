- [Editor(编辑器)](#editor编辑器)
  - [重要部分](#重要部分)
    - [InitializeOnLoad(类自动初始化)](#initializeonload类自动初始化)
    - [InitializeOnLoadMethod(静态方法自动初始化)](#initializeonloadmethod静态方法自动初始化)
    - [Cutsom Component Editor(自定义组件UI扩展脚本)](#cutsom-component-editor自定义组件ui扩展脚本)
    - [PrefabUtility](#prefabutility)
    - [PrefabUtility.SavePrefabAsset(GameObject go)](#prefabutilitysaveprefabassetgameobject-go)
  - [Editor界面扩展方法](#editor界面扩展方法)
    - [扩展菜单栏](#扩展菜单栏)
    - [添加弹窗](#添加弹窗)
    - [扩展Project Settings](#扩展project-settings)
    - [扩展Preferences](#扩展preferences)
    - [Hierarchy节点绘制](#hierarchy节点绘制)
    - [Inspector面板版头区域绘制](#inspector面板版头区域绘制)
    - [Inspector面板绘制](#inspector面板绘制)
    - [Scene面板绘制](#scene面板绘制)
      - [Handles](#handles)
        - [Handles.BeginGUI \&\& Handles.EndGUI(设置GUI区间)](#handlesbegingui--handlesendgui设置gui区间)
        - [HandleUtility.AddDefaultControl(启用Scene视图GUI交互)](#handleutilityadddefaultcontrol启用scene视图gui交互)
      - [Tools](#tools)
        - [Tools.current](#toolscurrent)
  - [GUI绘制（传统版本）](#gui绘制传统版本)
    - [UI Class(绘制UI用到的几个核心类)](#ui-class绘制ui用到的几个核心类)
      - [GUI](#gui)
      - [EditorGUI](#editorgui)
      - [GUILayout](#guilayout)
      - [EditorGUILayout](#editorguilayout)
      - [GUIStyle](#guistyle)
      - [GUILayout](#guilayout-1)
    - [PropertyField(属性值)](#propertyfield属性值)
    - [Space(空间)](#space空间)
    - [Box(区块)](#box区块)
    - [Label(文本标签)](#label文本标签)
    - [Button(按钮)](#button按钮)
    - [Foldout(折叠按钮)](#foldout折叠按钮)
    - [Texture(图片)](#texture图片)
    - [Toggle(单选框)](#toggle单选框)
    - [Toggle Group(多选组)](#toggle-group多选组)
    - [ToolBar(单选组)](#toolbar单选组)
    - [Selection Grid(单选组)](#selection-grid单选组)
    - [TextField(文本输入框)](#textfield文本输入框)
    - [ColorField(颜色输入框)](#colorfield颜色输入框)
    - [ObjectField(物件输入框)](#objectfield物件输入框)
    - [GenericMenu(菜单栏)](#genericmenu菜单栏)
    - [Slider(滑动条)](#slider滑动条)
    - [ScrollView(滑动组)](#scrollview滑动组)
    - [Horizontal(横向排序)](#horizontal横向排序)
    - [Vertical(纵向排序)](#vertical纵向排序)
  - [GUI绘制（UXML模式）](#gui绘制uxml模式)
    - [实际案例](#实际案例)
  - [操作行为](#操作行为)
    - [Event(当前交互信息)](#event当前交互信息)
      - [Event.current](#eventcurrent)
      - [Event.current.type](#eventcurrenttype)
        - [EventType.MouseDown](#eventtypemousedown)
        - [EventType.MouseUp](#eventtypemouseup)
        - [EventType.MouseDrag](#eventtypemousedrag)
        - [EventType.KeyDown](#eventtypekeydown)
        - [EventType.KeyUp](#eventtypekeyup)
        - [EventType.DragUpdated](#eventtypedragupdated)
        - [EventType.DragPerform](#eventtypedragperform)
        - [EventType.DragExited](#eventtypedragexited)
        - [EventType.Used](#eventtypeused)
      - [Event.current.isMouse](#eventcurrentismouse)
      - [Event.current.button](#eventcurrentbutton)
      - [Event.current.mousePosition](#eventcurrentmouseposition)
      - [Event.current.isKey](#eventcurrentiskey)
      - [Event.current.keyCode](#eventcurrentkeycode)
  - [操作行为对象](#操作行为对象)
    - [Selection(选中物体/节点)](#selection选中物体节点)
      - [Selection.count(选中节点数量)](#selectioncount选中节点数量)
      - [Selection.activeObject(选中文件)](#selectionactiveobject选中文件)
      - [Selection.objects(选中的所有文件)](#selectionobjects选中的所有文件)
      - [Selection.activeGameObject(选中预制)](#selectionactivegameobject选中预制)
      - [Selection.gameObjects(选中的所有预制)](#selectiongameobjects选中的所有预制)
    - [DragAndDrop(拖放物体/节点)](#draganddrop拖放物体节点)
      - [DragAndDrop.paths(当前拖放的所有文件路径)](#draganddroppaths当前拖放的所有文件路径)
      - [DragAndDrop.objectReferences(当前拖放的所有物体信息)](#draganddropobjectreferences当前拖放的所有物体信息)
      - [DragAndDrop.visualMode(拖放物体时显示的标签)](#draganddropvisualmode拖放物体时显示的标签)
      - [使用事例](#使用事例)
  - [常用接口](#常用接口)
    - [复制文本到剪贴板](#复制文本到剪贴板)
    - [弹出提示窗口](#弹出提示窗口)
    - [选择目录](#选择目录)
    - [选择文件](#选择文件)
    - [保存文件](#保存文件)


# Editor(编辑器)

## 重要部分

### InitializeOnLoad(类自动初始化)
当UnityEditor启动时,会自动执行带有[InitializeOnLoad]类的**静态**构造方法
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

### InitializeOnLoadMethod(静态方法自动初始化)
当UnityEditor启动时,会自动执行带有[InitializeOnLoadMethod]的**静态方法**
```C
public class MyEditorClass
{
    [InitializeOnLoadMethod]
    public static void Initialize()
    {
        Debug.Log("Function Initialize");
    }
}
```
![alt text](assets/unity_editor/image-51.png)

### Cutsom Component Editor(自定义组件UI扩展脚本)
声明自定义组件的界面扩展,命名为组件类名+Editor(如自定义组件为CustomComponent1,则对应的编辑器名称为CustomComponent1Editor),可以在该脚本中扩展Scene面板和Inspector面板

继承UnityEditor.Editor
![Alt text](assets/unity_editor/image.png)
添加CustomEditor特性
![Alt text](assets/unity_editor/image-1.png)

### PrefabUtility
###  PrefabUtility.SavePrefabAsset(GameObject go)
在Editor模式下修改GameObject后,使用该命令将变动保存到硬盘


## Editor界面扩展方法

### 扩展菜单栏
声明静态方法,给方法添加MenuItem属性及菜单路径
如下例子，添加成功后，在Editor最上方菜单栏就会展示新增菜单
```C
[MenuItem("CustomMenu/SubMenu")]
public static void Menu1()
{

}
```
![alt text](assets/unity_editor/image-44.png)

也可以在现菜单上添加新的选项
如下例子，在Assets菜单内，会多出一个SubMenu选项。此外，如果在Project窗口内右键点击(或者右键点击Project窗口内的文件)，出现的菜单栏里也会多出一个SubMenu选项,因为这里展示的其实是Assets菜单栏
```C
[MenuItem("Assets/SubMenu")]
public static void Menu2()
{
    
}
```
![alt text](assets/unity_editor/image-45.png)
![alt text](assets/unity_editor/image-46.png)
如下例子，不仅在GameObject菜单里会出现SubMenu。此外，在Hierarchy窗口内右键点击（或者右键点击Hierarchy窗口内的节点），出现的菜单栏里也会多出一个SubMenu选项,因为这里展示的其实是GameObject菜单栏
```C
[MenuItem("GameObject/SubMenu")]
public static void Menu3()
{
    
}
```
![alt text](assets/unity_editor/image-47.png)
![alt text](assets/unity_editor/image-48.png)

### 添加弹窗
创建一个继承EditorWindow的自定义弹窗类来实现弹窗的绘制
绘制实例
```C
//自定义弹窗类,需要继承EditorWindow
public class CustomWindow : EditorWindow
{
    //在Window菜单栏内添加打开弹窗的选项
    [MenuItem("Window/CustomWindow")]
    public static void CreateWindow()
    {
        //创建默认弹窗,标题为Window Title
        GetWindow<CustomWindow>("Window Title");
    }
    
    //绘制弹窗内容
    private void OnGUI()
    {
        EditorGUILayout.LabelField("This is a Window");
    }
}
```
![alt text](assets/unity_editor/image-49.png)
![alt text](assets/unity_editor/image-50.png)


### 扩展Project Settings
绘制实例
```C
public class MySetting
{
    [SettingsProvider]
    private static SettingsProvider CreateProjectSetting()
    {
        var settingView = new SettingsProvider("MyProjectSetting/", SettingsScope.Project)
        {
            label = "Project Setting Title",
            guiHandler = CreateProjectSetting,
        };
        return settingView;
    }

    private static void CreateProjectSetting(string _)
    {
        EditorGUILayout.TextField("My Project Setting");
    }
}
```
![alt text](assets/unity_editor/image-52.png)

### 扩展Preferences
绘制实例
```C
public class MySetting
{
    [SettingsProvider]
    private static SettingsProvider CreatePreferenceSetting()
    {
        var settingView = new SettingsProvider("MyPreferenceSetting/", SettingsScope.User)
        {
            label = "Preference Setting Title",
            guiHandler = CreatePreferenceSetting,
        };
        return settingView;
    }

    private static void CreatePreferenceSetting(string _)
    {
        EditorGUILayout.TextField("My Preference Setting");
    }
}
```
![alt text](assets/unity_editor/image-53.png)

### Hierarchy节点绘制
可以通过注册EditorApplication.hierarchyWindowItemOnGUI来绘制Hierarchy中物体节点的额外信息
添加注册
```C
private void OnEnable()
{
    EditorApplication.hierarchyWindowItemOnGUI += OnHierarchyWindowItemOnGUI;
}
```
取消注册
```C
private void OnDisable()
{
    EditorApplication.hierarchyWindowItemOnGUI -= OnHierarchyWindowItemOnGUI;
}
```
绘制实例
```C
//OnHierarchyWindowItemOnGUI函数被调用的次数等于Hierarchy里所有节点的数量
static void OnHierarchyWindowItemOnGUI(int instanceId, Rect selectionRect)
{
    //instanceId为当前物体节点的InstanceID，通过这个Id获取到物体的信息
    Object obj = EditorUtility.InstanceIDToObject(instanceId);
    var go = obj as GameObject;
    if (go == null)
    {
        return;
    }
    
    var component = go.GetComponent<CustomComponent1>();
    if (component == null)
    {
        return;
    }
    
    //selectionRect为当前物体节点的坐标范围
    var rect = new Rect(selectionRect.x + 120, selectionRect.y, 20, 20);
    var tex =  AssetDatabase.LoadAssetAtPath<Texture2D>("Assets/RawResource/Package/ui_icon_01.png");
    GUI.DrawTexture(rect, tex);
}
```
如下图，OnHierarchyWindowItemOnGUI函数会被调用4次，依次传入Main Camera,Directional Light,EventSystem,GameObject这4个节点的instanceId。因为只有GameObject持有CustomComponent1组件，所以在这个节点上绘制图标
![alt text](assets/unity_editor/image-41.png)

### Inspector面板版头区域绘制
可以通过注册Editor.finishedDefaultHeaderGUI来实现Inspector的版头区域的GUI绘制
添加注册
```C
private void OnEnable()
{
    UnityEditor.Editor.finishedDefaultHeaderGUI += OnFinishedDefaultHeaderGUI;
}
```
取消注册
```C
private void OnDisable()
{
    UnityEditor.Editor.finishedDefaultHeaderGUI -= OnFinishedDefaultHeaderGUI;
}
```
绘制实例
```C
void OnFinishedDefaultHeaderGUI(UnityEditor.Editor editor)
{
    //editor.target是当前Inspector面板里显示信息的物体
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

### Inspector面板绘制
在继承UnityEditor.Editor的脚本中,声明函数OnInspectorGUI
在函数内声明绘制UI方法
```C
public void OnInspectorGUI()
{      
  GUI.Box(new Rect(100, 0, 100, 100), "Box 1");
  GUILayout.Box("Box 2", GUILayout.Width(100), GUILayout.Height(100));
}
```
![alt text](assets/unity_editor/image-3.png)

### Scene面板绘制
在继承UnityEditor.Editor的脚本中,声明函数OnSceneGUI
要绘制UI需要在Handles.BeginGUI和Handles.EndGUI之间,因此需要先声明这两个方法,最后在方法区间声明绘制UI方法
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

##### Handles.BeginGUI && Handles.EndGUI(设置GUI区间)
在Scene内绘制GUI需要在这2个命令之间
![Alt text](assets/unity_editor/image-4.png)

##### HandleUtility.AddDefaultControl(启用Scene视图GUI交互)
当无法选中Scene视图内的GUI时，可以执行此命令
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

## GUI绘制（传统版本）

### UI Class(绘制UI用到的几个核心类)
#### GUI 
用于绘制基本的用户界面元素,如按钮、文本框等。需要手动管理布局和位置

#### EditorGUI 
扩展了GUI类,提供了一些额外的编辑器特定控件和功能

#### GUILayout 
用于绘制基本的用户界面元素,如按钮、文本框等。提供了自动布局的功能,无需手动管理控件的位置和大小

#### EditorGUILayout 
扩展了GUILayout类,提供了一些额外的编辑器特定控件和功能

#### GUIStyle 
定义GUI样式(GUIStyle.fontSize/GUIStyle.normal.textColor等),可在上面创建GUI时作为参数传入

#### GUILayout 
定义GUI样式(GUILayout.Width/GUILayout.Height等), 可在GUILayout/EditorGUILayout创建GUI时作为GUILayoutOption参数传入

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

### PropertyField(属性值)
Unity根据当前属性类型自动绘制UI,属性类型为Class时,需要添加Serializable标签
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

### Space(空间)
增加UI之间的空间
```C
GUILayout.Space(100);
EditorGUILayout.Space(100);
```
![alt text](assets/unity_editor/image-11.png)

### Box(区块)
```C
GUI.Box(new Rect(0, 0, 100, 100), "Box 1");
GUILayout.Box("Box 2", GUILayout.Width(100), GUILayout.Height(100));
```
![alt text](assets/unity_editor/image-26.png)

### Label(文本标签)
```C
GUI.Label(new Rect(0, 0, 100, 30), "Label 1");
EditorGUI.LabelField(new Rect(0, 30, 100, 30), "Label 2");
GUILayout.Label("Label 3", GUILayout.Width(100), GUILayout.Height(50));
EditorGUILayout.LabelField("Label 4", GUILayout.Width(100),GUILayout.Height(30));
```
![alt text](assets/unity_editor/image-12.png)

### Button(按钮)
```C
m_Button = GUI.Button(new Rect(150, 0, 100, 30), "Button 1");
m_Button = EditorGUI.DropdownButton(new Rect(150, 50, 100, 50), new GUIContent("Button 2"), FocusType.Passive);
m_Button = GUILayout.Button("Button 3", GUILayout.Width(100), GUILayout.Height(30));
m_Button = EditorGUILayout.DropdownButton(new GUIContent("Button 4"), FocusType.Passive, GUILayout.Width(100), GUILayout.Height(30));
```
![alt text](assets/unity_editor/image-17.png)

### Foldout(折叠按钮)
```C
isFold = EditorGUILayout.Foldout(isFold, "Foldout Button");
if (isFold)
{
    EditorGUILayout.TextArea("1111111111111");
}
```
![alt text](assets/unity_editor/image-62.png)

### Texture(图片)
```C
var rect = new Rect(0, 0, 20, 20);
var texture = AssetDatabase.LoadAssetAtPath<Texture2D>("Assets/RawResource/Package/ui_icon_01.png");
GUI.DrawTexture(rect, texture);

rect = new Rect(0, 40, 20, 20);
texture = AssetDatabase.LoadAssetAtPath<Texture2D>("Assets/RawResource/Package/ui_icon_02.png");
EditorGUI.DrawTextureAlpha(rect, texture);
```
![alt text](assets/unity_editor/image-40.png)

### Toggle(单选框)
```C
m_Toggle = GUI.Toggle(new Rect(0, 0, 100, 30), m_Toggle, "Toggle 1");
m_Toggle = GUILayout.Toggle(m_Toggle, "Toggle 2", GUILayout.Width(70), GUILayout.Height(70));
m_Toggle = EditorGUILayout.Toggle("Toggle 3", m_Toggle);
m_Toggle = EditorGUILayout.ToggleLeft("Toggle 4", m_Toggle);
```
![alt text](assets/unity_editor/image-13.png)

### Toggle Group(多选组)
```C
using (var toggleGroup = new EditorGUILayout.ToggleGroupScope("Toggle Group", m_Toggle))
{
    m_Toggle = toggleGroup.enabled;
    m_Toggle1 = EditorGUILayout.Toggle("Toggle 1", m_Toggle1);
    m_Toggle2 = EditorGUILayout.Toggle("Toggle 2", m_Toggle2);
}
```
![alt text](assets/unity_editor/image-14.png)

### ToolBar(单选组)
排列一行
```C
var grids = new string[] {"Toggle 1", "Toggle 2", "Toggle 3", "Toggle 4"};
m_ToolBarIndex = GUILayout.Toolbar(m_ToolBarIndex, grids);
```
![alt text](assets/unity_editor/image-16.png)

### Selection Grid(单选组)
可设置排列多行
```C
var grids = new string[] {"Toggle 1", "Toggle 2", "Toggle 3", "Toggle 4"};
m_SelectionGrid = GUILayout.SelectionGrid(m_SelectionGrid, grids, 2, GUILayout.Width(200));
```
![alt text](assets/unity_editor/image-15.png)

### TextField(文本输入框)
```C
m_TextField = GUI.TextField(new Rect(200, 0, 200, 30), m_TextField);
m_TextField = EditorGUI.TextField(new Rect(200, 50, 300, 30),"TextField 1", m_TextField);
m_TextField = GUILayout.TextField(m_TextField, GUILayout.Width(200), GUILayout.Height(30));
m_TextField = EditorGUILayout.TextField("TextField 2", m_TextField, GUILayout.Width(300), GUILayout.Height(30));
```
![alt text](assets/unity_editor/image-18.png)

### ColorField(颜色输入框)
```C
m_Color = EditorGUI.ColorField(new Rect(200, 0, 200, 20), m_Color);
m_Color = EditorGUILayout.ColorField("ColorField 1", m_Color);
```
![alt text](assets/unity_editor/image-19.png)

### ObjectField(物件输入框)
```C
EditorGUI.ObjectField(new Rect(0, 0, 400, 20), m_GameObject);
m_Object = EditorGUILayout.ObjectField("ObjectField 1", m_Object, typeof(UnityEngine.Object));
```
![alt text](assets/unity_editor/image-20.png)

### GenericMenu(菜单栏)
```C
//右键点击
if (Event.current.button == (int)MouseButton.RightMouse)
{
    GenericMenu menu = new GenericMenu();
    //一级菜单栏
    menu.AddItem(new GUIContent("Menu_1"), false, () =>
    {
        Debug.Log("点击Menu_1");
    });
    //二级菜单栏
    menu.AddItem(new GUIContent("SubMenu/Menu_2"), true, () =>
    {
        Debug.Log("点击Menu_2");
    });
    menu.ShowAsContext();
}
```
![alt text](assets/unity_editor/image-42.png)

### Slider(滑动条)
```C
m_SliderValue = GUI.HorizontalSlider(new Rect(0, 0, 300, 20),m_SliderValue, 0, 99);
m_SliderValue = EditorGUI.Slider(new Rect(0, 30, 300, 20),m_SliderValue, 0, 99);
m_SliderValue = GUILayout.HorizontalSlider(m_SliderValue, 0, 99);
m_SliderValue = EditorGUILayout.Slider(m_SliderValue, 0, 99);
```
![alt text](assets/unity_editor/image-21.png)

### ScrollView(滑动组)
```C
m_ScrollPos = EditorGUILayout.BeginScrollView(m_ScrollPos, GUILayout.Width(100), GUILayout.Height(100));
for (int i = 0; i < 20; i++)
{
    EditorGUILayout.LabelField("Label " + i);
}
EditorGUILayout.EndScrollView();
```
滑动内容需要创建于BeginScrollView到EndScrollView区间,并且宽/高超出滑动组范围,则会自动显示水平/垂直滑动条并可拖动
![alt text](assets/unity_editor/image-22.png)

### Horizontal(横向排序)
使用GUILayout、EditorGUILayout创建的UI默认为从上到下的纵向排序,可以将创建的UI元素包裹在EditorGUILayout.BeginHorizontal和EditorGUILayout.EndHorizontal之间,则会变成从左到右的横向排序
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
也可以将其作用在滑动组内容上,将其变为横向滑动组
![alt text](assets/unity_editor/image-24.png)

### Vertical(纵向排序)
使用GUILayout、EditorGUILayout创建的UI默认就为纵向排序,因此一般是搭配横向排序来嵌套使用
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

## GUI绘制（UXML模式）
UXML模式，将UI整体绘制划分为3个文件
C#文件，UI逻辑处理与动态UI加载
UXML文件，静态UI绘制
USS文件，UI样式设置（基本上所有UI元素都持有的属性设置，比如位置长宽颜色等）
[UXML 官方文档](https://docs.unity3d.com/Manual/UIE-UXML.html)
[UXML UI元素 官方文档](https://docs.unity3d.com/Manual/UIE-ElementRef.html)
[USS 官方文档](https://docs.unity3d.com/Manual/UIE-USS.html)
[USS 样式属性 官方文档](https://docs.unity3d.com/Manual/UIE-USS-SupportedProperties.html)
[USS 样式属性数据类型 官方文档](https://docs.unity3d.com/Manual/UIE-USS-PropertyTypes.html)

### 实际案例
右键点击创建UXML模式文件
![alt text](assets/unity_editor/image-55.png)
确认后会出现3个相同命名的文件，分别为上面提及的.cs .uxml .uss文件
![alt text](assets/unity_editor/image-56.png)
结果弹窗展示
![alt text](assets/unity_editor/image-57.png)

.cs文件
```C#
public void CreateGUI()
{
    VisualElement root = rootVisualElement;

    // 导入UXML文件
    var visualTree = AssetDatabase.LoadAssetAtPath<VisualTreeAsset>("Assets/Editor/EditorWindow/UXMLWindow.uxml");
    VisualElement labelFromUXML = visualTree.Instantiate();
    root.Add(labelFromUXML);

    // 导入uss样式文件
    var styleSheet = AssetDatabase.LoadAssetAtPath<StyleSheet>("Assets/Editor/EditorWindow/UXMLWindow.uss");
    root.styleSheets.Add(styleSheet);
    
    //菜单按钮
    ToolbarButton menuButton1 = root.Query<ToolbarButton>("toolbar-button-1");
    menuButton1.clicked += () =>
    {
        Debug.Log("菜单按钮1");
    };
    ToolbarButton menuButton2 = root.Query<ToolbarButton>("toolbar-button-2");
    menuButton2.clicked += () =>
    {
        Debug.Log("菜单按钮2");
    };
    
    //下拉菜单
    ToolbarMenu toolbarMenu = root.Query<ToolbarMenu>("toolbar-menu");
    //下拉菜单选项
    toolbarMenu.menu.AppendAction("菜单选项_1", action =>
    {
        menuButton1.text = "菜单_2_1";
        menuButton2.text = "菜单_2_2";
    });
    toolbarMenu.menu.AppendAction("菜单选项_2", action =>
    {
        menuButton1.text = "菜单_3_1";
        menuButton2.text = "菜单_3_2";
    });
    
    //可视按钮
    Button visualButton = root.Query<Button>("visual-button");
    visualButton.clicked += () =>
    {
        Debug.Log("可视按钮");
    };
    
    //滑动列表
    ListView listView = root.Query<ListView>("list-view");
    
    //子项高度（必要）
    listView.itemHeight = 50;
    
    //列表数据源（必要）
    string[] listSource = new []{"列表项_1", "列表项_2", "列表项_3", "列表项_4", "列表项_5"};
    listView.itemsSource = listSource;
    
    //列表子项创建
    listView.makeItem = () =>
    {
        VisualElement item = new VisualElement();
        //添加uss文件里的ListItem样式，子项内容改为横向排序
        item.AddToClassList("ListItem");
        
        Label label = new Label();
        label.AddToClassList("ListItemLabel");
        item.Add(label);
        
        Button button = new Button();
        button.AddToClassList("ListItemButton");
        item.Add(button);

        return item;
    };
    
    //列表子项更新（数据绑定）
    listView.bindItem = (item, index) =>
    {
        //对整个子项添加右键点击
        item.AddManipulator(new ContextualMenuManipulator(evt =>
        {
            //弹出菜单栏1
            evt.menu.AppendAction("右键菜单_1", action =>
            {
                Debug.Log("menu_1");
            });
            
            //弹出菜单栏2
            evt.menu.AppendAction("右键菜单_2", action =>
            {
                Debug.Log("menu_2");
            });
        }));
        
        //子项文本
        Label label = item.Query<Label>();
        label.text = listSource[index];
        
        //子项按钮
        Button button = item.Query<Button>();
        button.text = $"按钮_{index}";
        button.clicked += () =>
        {
            Debug.Log($"列表项_{index}");
        };
    };
}
```
.uxml文件
```
<?xml version="1.0" encoding="utf-8"?>
<engine:UXML
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xmlns:engine="UnityEngine.UIElements"
    xmlns:editor="UnityEditor.UIElements"
    xsi:noNamespaceSchemaLocation="../../../UIElementsSchema/UIElements.xsd"
>
    <editor:Toolbar name="toolbar">
        <!-- 菜单按钮_1 -->
        <editor:ToolbarButton name="toolbar-button-1" text="菜单_1_1" class="ToolbarButton" />
        <!-- 菜单按钮_2 -->
        <editor:ToolbarButton name="toolbar-button-2" text="菜单_1_2" class="ToolbarButton" />
        <!-- 菜单展开选项 -->
        <editor:ToolbarMenu name="toolbar-menu" text="展开菜单" />
    </editor:Toolbar>
    <!-- 横向排序 -->
    <engine:VisualElement style="flex-direction: row">
        <!-- 文本 居中 -->
        <engine:Label name="visual-label" text="标签" style="-unity-text-align: middle-center"/>
        <!-- 按钮 距离左侧文本间距10px -->
        <engine:Button name="visual-button" text="按钮" style="margin-left: 20px"/>
    </engine:VisualElement>
    <!-- 滑动列表 -->
    <engine:ListView name="list-view" class="ListView" />
</engine:UXML>
```
.uss文件
```
.ToolbarButton {
    width: 70px;
    -unity-text-align: middle-center;
}
.ListView {
    width: 100%;
    height: 200;
    background-color: rgba(0,0,0,0.1);
}
.ListItem {
    flex-direction: row;
}
.ListItemButton {
    width: 150px;
}
.ListItemLabel {
    -unity-text-align: middle-left;
    color: aqua;
}
```

## 操作行为
在Editor模式下,可以通过Event获取当前操作的行为信息

### Event(当前交互信息)
可以通过Event.current获取到当前外设(鼠标、键盘)正在执行的交互事件(点击、滑动等)

#### Event.current
当前正在执行的交互事件

#### Event.current.type
当前交互事件类型EventType,通过该类型判断目前正在进行什么交互
![alt text](assets/unity_editor/image-43.png)
常见的事件列举
##### EventType.MouseDown
鼠标按下
##### EventType.MouseUp
鼠标松开
##### EventType.MouseDrag
鼠标移动
##### EventType.KeyDown
键盘按下
##### EventType.KeyUp
键盘松开
##### EventType.DragUpdated
物件拖拽中,正在拖拽某个文件/节点才会触发
##### EventType.DragPerform
物件拖拽到适合区域,需要设置DragAndDrop.visualMode才会触发
##### EventType.DragExited
物件拖拽操作取消/结束
##### EventType.Used
事件已被执行,当调用Event.current.Use()后,Event.current.type会自动变为这个

#### Event.current.isMouse
是否触发鼠标点击

#### Event.current.button 
触发点击的鼠标部分,对应枚举MouseButton中的LeftMouse(左键)、RightMouse(右键)、MiddleMouse(滚轮)
没有操作的情况下,默认值为0(MouseButton.LeftMouse),因此不能单纯通过该值判断是否有点击触发

#### Event.current.mousePosition
当前鼠标所在位置

#### Event.current.isKey
是否触发键盘点击

#### Event.current.keyCode
触发键盘点击的按键,对应枚举KeyCode
没有操作的情况下,默认值为None

## 操作行为对象
获取操作行为所对应的文件/物体/节点

### Selection(选中物体/节点)
可以通过Selection获取在Hierarchy和Project窗口中选中的节点信息

#### Selection.count(选中节点数量)
![alt text](assets/unity_editor/image-28.png)

#### Selection.activeObject(选中文件)
包含Hierarchy和Project里的所有文件类型,如果选中复数物体的话,获取到的则是其中之一的信息
![alt text](assets/unity_editor/image-29.png)

#### Selection.objects(选中的所有文件)
包含Hierarchy和Project里的所有文件类型,可以获取到所有选中物体的信息
![alt text](assets/unity_editor/image-30.png)

#### Selection.activeGameObject(选中预制)
包含Hierarchy展示的预制节点和Project里的预制体,如果选中复数预制的话,获取到的则是其中之一的信息(选中的如果是其他文件类型则activeGameObject=null)
![alt text](assets/unity_editor/image-31.png)

#### Selection.gameObjects(选中的所有预制)
包含Hierarchy展示的预制节点和Project里的预制体,可以获取到所有选中预制的信息(选中的如果是其他文件类型则对应的GameObject=null)
![alt text](assets/unity_editor/image-32.png)

### DragAndDrop(拖放物体/节点)
可以通过DragAndDrop获取当前从Hierarchy和Project窗口中拖拽的节点信息

#### DragAndDrop.paths(当前拖放的所有文件路径)
包含Project里的所有文件类型,可以获取到所有拖拽文件的路径
![alt text](assets/unity_editor/image-33.png)

#### DragAndDrop.objectReferences(当前拖放的所有物体信息)
包含Hierarchy的所有物体及Project里的所有文件类型,可以获取到所有拖拽物体的信息
![alt text](assets/unity_editor/image-34.png)

#### DragAndDrop.visualMode(拖放物体时显示的标签)
当拖放物体时显示的小图标类型,可以设置该值作为对应区域是否可拖放的提示,对应枚举DragAndDropVisualMode

#### 使用事例
当物体被拖拽到特定区域时,打印物体信息
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


## 常用接口

### 复制文本到剪贴板
```c#
GUIUtility.systemCopyBuffer = "复制内容";
```

### 弹出提示窗口
```c#
EditorUtility.DisplayDialog("弹窗标题", "弹窗内容", "确认");
```
![alt text](assets/unity_editor/image-58.png)

### 选择目录
```c#
//返回目录完整路径
string folderPath = EditorUtility.OpenFolderPanel("选择目录", "Assets", "");
```
![alt text](assets/unity_editor/image-59.png)

### 选择文件
```c#
//返回文件完整路径
string filePath = EditorUtility.OpenFilePanel("选择文件", "Assets", "png");
```
![alt text](assets/unity_editor/image-61.png)

### 保存文件
```c#
//返回文件保存路径
string filePath = EditorUtility.SaveFilePanel("保存文件", "Assets", "文件名", "后缀");
//文件内容
List<string> content = new List<string>();
//路径不为空时
if (!string.IsNullOrEmpty(filePath))
{
    //向这个路径写入内容
    File.WriteAllLines(filePath, content);
}
```
![alt text](assets/unity_editor/image-60.png)