- [Unity Game（Unity开发）](#unity-gameunity开发)
  - [Memory（内存）](#memory内存)
    - [Managed Heap（托管堆）](#managed-heap托管堆)
      - [管理对象](#管理对象)
      - [管理机制](#管理机制)
  - [C# Unmanaged Memory（非托管C#对象）](#c-unmanaged-memory非托管c对象)
      - [注意事项](#注意事项)
    - [Native Heap（原生堆）](#native-heap原生堆)
      - [管理对象](#管理对象-1)
      - [管理机制](#管理机制-1)
      - [注意事项](#注意事项-1)
    - [Stack（栈）](#stack栈)
      - [管理对象](#管理对象-2)
      - [管理机制](#管理机制-2)
    - [GPU Memory（显存）](#gpu-memory显存)
      - [管理对象](#管理对象-3)
      - [管理机制](#管理机制-3)
      - [注意事项](#注意事项-2)
    - [Code Heap（代码堆）](#code-heap代码堆)
  - [Coroutine（协程）](#coroutine协程)
    - [说明](#说明)
    - [使用](#使用)
      - [开始协程](#开始协程)
      - [终止协程](#终止协程)
      - [yield](#yield)
      - [嵌套协程](#嵌套协程)
  - [Job System](#job-system)
    - [说明](#说明-1)
    - [声明Job结构](#声明job结构)
      - [主体](#主体)
      - [成员](#成员)
        - [blittable](#blittable)
        - [NativeContainer](#nativecontainer)
        - [Execute()](#execute)
    - [执行Job](#执行job)
      - [声明需要的数据类型](#声明需要的数据类型)
        - [NativeArray](#nativearray)
      - [开始执行](#开始执行)
    - [多Job依赖执行](#多job依赖执行)
      - [依赖执行](#依赖执行)
    - [Job For](#job-for)
  - [Burst编译器 + JobSystem](#burst编译器--jobsystem)
    - [安装设置](#安装设置)
    - [使用](#使用-1)


# Unity Game（Unity开发）

## Memory（内存）
[Memory in Unity introduction](https://docs.unity3d.com/Manual/performance-memory.html)
Unity项目运行时，资源内存主要分为5个部分。Managed Heap（托管堆），Native Heap（原生堆），Stack（栈），GPU Memory（显存），Code Heap（代码堆）

### Managed Heap（托管堆）
自动化管理内存堆
#### 管理对象
游戏内的C#类型实例，内部字段，引用类型对象
#### 管理机制
由Mono/il2cpp后台自动管理，创建时自动分配内存大小，并添加对象引用。销毁时去除对象引用。会定时扫描内存堆（GC 垃圾回收），标记失去引用的对象并释放对应的内存空间
## C# Unmanaged Memory（非托管C#对象）
[Unmanaged C# memory](https://docs.unity3d.com/Manual/performance-unmanaged-memory.html)
在Unity.Collections命名空间下创建的类型为非托管类型，不会受Unity的托管机制影响。这些对象需要开发者手动触发Dispose进行内存释放
#### 注意事项
频繁创建删除物体会出现内存碎片（内存地址断断续续），增大内存消耗（创建物体时找不到足以容纳的空间地址）并造成GC卡顿（GC时会暂停CPU执行其他工作）。对频繁创建删除的物体可使用对象池之类的手段进行缓存，减少装箱拆箱（值类型、引用类型转换）

### Native Heap（原生堆）
手动管理内存堆
#### 管理对象
Unity原生C++/C脚本（Transform、Render等原生组件），GameObject，纹理图，音效等
#### 管理机制
资源需要手动进行加载卸载管理，大部分属于Unity原生对象
#### 注意事项
由于需要开发者自己管理，容易忽视未使用资源导致内存泄漏

### Stack（栈）
后进先出的内存地址，空间很小并且固定大小
#### 管理对象
函数内创建的临时变量（值类型、引用类型的对象引用）
#### 管理机制
作用域内被创建，一旦出了作用域就会被立即卸载回收

### GPU Memory（显存）
GPU纹理内存堆
#### 管理对象
上传到显存的纹理贴图
#### 管理机制
上传后内存扔持有对应纹理图的引用，当引用被销毁后会被回收
#### 注意事项
一般纹理图上传到显存后，内存中的纹理图占用就会被回收。但是如果勾选纹理图Inspector属性中的Read/Write Enabled，它就还会保持在内存中，造成一份贴图2份内存占用

### Code Heap（代码堆）
存储的是编译后对应平台机器码，一般不需要开发者关心

## Coroutine（协程）

### 说明
协程同样运行在主线程上，但和主任务异步进行。一般当有某些任务需要分段完成（而不希望在一帧内做完）的时候需要用到

### 使用
#### 开始协程
```c#
IEnumerator enumerator = CreateObject();
StartCoroutine(enumerator);
```
调用StartCoroutine函数创建协程，参数是一个迭代器。Unity每帧会使用迭代器MoveNext访问当前存储的Current 一般也就是yield return所创建的YieldInstruction
#### 终止协程
协程在对应脚本unable时并不会停止，只有执行结束或者对应脚本绑定Go被销毁才会终止。
如果想要手动终止协程可以使用
```c#
IEnumerator enumerator = CreateObject();
StopCoroutine(enumerator);
```
#### yield
涉及到协程，一般都会用到yield return命令
可以停止该协程这帧代码的执行，并在下一个时间段继续执行剩下的代码
```c#
IEnumerator CreateObjects()
    {
        //开启嵌套协程
        IEnumerator enumerator = CreateObjects2();
        yield return StartCoroutine(enumerator);
        
        //下一帧继续
        yield return null;
        //创建GameObject A
        new GameObject("A");

        //0.5秒后继续
        yield return new WaitForSeconds(0.5f);
        //创建GameObject B
        new GameObject("B");
    }
```
需要注意的是，yield中断代码到下次执行的时候，是在每帧Update之后，LateUpdate之前
![Alt text](assets/unity_game/image-3.png)
#### 嵌套协程
可以通过yield return StartCoroutine(IEnumerator)开启嵌套协程。开启后会在<font color=#FF0000>同一帧</font>进入到对应协程的方法，直到<font color=#FF0000>嵌套协程执行结束</font>才会回到主协程继续执行剩余的任务
```c#
void Start()
    {
        IEnumerator enumerator = CreateObjects();
        StartCoroutine(enumerator);
        //StopCoroutine(enumerator);
    }

    IEnumerator CreateObjects()
    {
        //开启嵌套协程
        IEnumerator enumerator = CreateObjects2();
        yield return StartCoroutine(enumerator);
        
        //下一帧继续
        yield return null;
        //创建GameObject A
        new GameObject("A");

        //0.5秒后继续
        yield return new WaitForSeconds(0.5f);
        //创建GameObject B
        new GameObject("B");
    }
    
    IEnumerator CreateObjects2()
    {
        //1秒后继续
        yield return new WaitForSeconds(1f);
        //创建GameObject C
        new GameObject("C");
    }
```
创建顺序是"C" "A" "B"

## Job System

### 说明
Unity提供的多线程技术，是Unity Dots(多线程运行框架)的核心组件之一，可以将任务分给多个线程并行执行，以此提高运行效率</br>
[Unity学习—JobSystem](https://zhuanlan.zhihu.com/p/148160780)<br/>
[什么是C# Job System？](https://developer.unity.cn/projects/61f68b70edbc2a16f7df9e83)

### 声明Job结构
一个Job代表一个可执行的任务</br>
```c#
public struct JobPlus : IJob
{
    public float a;
    public float b;
    public NativeArray<float> result;
    
    public void Execute()
    {
        result[0] = a + b;
    }
}
```
#### 主体
创建的Job主体是一个实现了IJob接口的结构体</br>
#### 成员
Job内的成员仅支持blittable及NativeContainer类型
##### blittable
指的是托管代码(中间语言IL码)和本机代码（计算机执行码）具有相同位级别表示形式的类型。因此传送中无需将该类型转换为不同的格式</br>
例:</br>
1.byte, sbyte, short, ushort, int, uint, long, ulong, single, double</br>
2.blittable类型的一维数组,如:int[]</br>
##### NativeContainer
一种托管值类型。里面包含了指向Native内存的指针，非c#托管内存。当与JobSystem一起使用时，NativeContainer允许Job访问与主线程共享的数据，而非数据副本
##### Execute()
该任务的具体执行函数，继承IJob接口后必须实现
### 执行Job
#### 声明需要的数据类型
```c#
float a = 10;
float b = 10;
NativeArray<float> result = new NativeArray<float>(1, Allocator.Persistent);
var jobPlus = new JobPlus
{
    a = a,
    b = b,
    result = result,
};
```
##### NativeArray
NativeContainer的一种</br>
参数1是数组长度</br>
参数2是存在时长</br>
#### 开始执行
```c#
var handlePlus = jobPlus.Schedule();
handlePlus.Complete();
Debug.Log($"{result[0]}");
result.Dispose();
```
var handlePlus = jobPlus.Schedule()</br>
执行任务并返回句柄</br>
handlePlus.Complete()</br>
使主线程等待任务完成再继续（由于是多线程，结束时间不确定）</br>
Debug.Log($"{result[0]}")</br>
访问native层的数组并输出计算结果，20</br>
result.Dispose()</br>
释放result数组内存(NativeContainer内存需要手动管理)</br>
### 多Job依赖执行
```c#
//声明Native数组，长度为1，并且长久保持
NativeArray<float> result = new NativeArray<float>(1, Allocator.Persistent);
//声明第一个任务，加法
var jobPlus = new JobPlus
{
    a = 10,
    b = 10,
    result = result,
};
//声明第二个任务，减法
var jobMinus = new JobMinus()
{
    a = 5,
    result = result,
};
//执行加法任务
var handlePlus = jobPlus.Schedule();
//执行减法任务(当加法任务执行完成后才执行)
var handleMinus = jobMinus.Schedule(handlePlus);
//当减法任务完成后，主线程继续执行
handleMinus.Complete();
//输出结果
Debug.Log($"{result[0]}");
//释放内存
result.Dispose();
```
#### 依赖执行
执行job.Schedule中可以传入别的任务句柄，这样当前任务就只有当依赖任务执行结束后才会执行(避免多线程执行时间不确定)
### Job For
```c#
public struct VelocityJob : IJobFor
{
    //不需要写入的可以标记为只读数据，优化效率
    [ReadOnly]
    public NativeArray<Vector3> velocity;
    public NativeArray<Vector3> position;
    public float deltaTime;

    public void Execute(int i)
    {
        position[i] = position[i] + velocity[i] * deltaTime;
    }
}

private void JobParallel()
{
    //声明原生数组position
    var position = new NativeArray<Vector3>(500, Allocator.Persistent);

    //声明原生数组velocity
    var velocity = new NativeArray<Vector3>(500, Allocator.Persistent);
    for (int i = 0; i < velocity.Length; i++)
    {
        velocity[i] = new Vector3(0, 10, 0);
    }
    
    var job = new VelocityJob()
    {
        deltaTime = Time.deltaTime,
        position = position,
        velocity = velocity,
    };
    //运行在主线程(参数为Execute总执行次数)
    job.Run(position.Length);
    //运行在单个工作线程(参数1为Execute总执行次数 参数2为需要等待的依赖任务)
    JobHandle scheduleJobHandle = job.Schedule(position.Length, default);
    //并行在多个工作线程(参数1为Execute总执行次数 参数2为单批次执行次数 参数3为需要等待的依赖任务)
    JobHandle scheduleParallelJobHandle = job.ScheduleParallel(position.Length, 64, scheduleJobHandle);
    //等待并行任务结束
    scheduleParallelJobHandle.Complete();
    
    Debug.Log(job.position[0]);

    position.Dispose();
    velocity.Dispose();
}
```

## Burst编译器 + JobSystem
Unity提供的一款编译器，可以将c#代码直接编译为对应的机器码以提升运行效率。配合JobSystem使用
### 安装设置
![](assets/unity_game/image.png)</br>
在Package Manager中找到Burst包，进行安装
### 使用
执行代码
```
private void JobParallelTimeCost()
{
    //声明原生数组position
    var position = new NativeArray<Vector3>(500000, Allocator.Persistent);

    //声明原生数组velocity
    var velocity = new NativeArray<Vector3>(500000, Allocator.Persistent);
    for (int i = 0; i < velocity.Length; i++)
    {
        velocity[i] = new Vector3(0, 10, 0);
    }
    
    var job = new VelocityJob()
    {
        deltaTime = Time.deltaTime,
        position = position,
        velocity = velocity,
    };

    var startTime = Time.realtimeSinceStartup;
    
    JobHandle scheduleJobHandle = job.Schedule(position.Length, default);
    scheduleJobHandle.Complete();
    
    Debug.Log($"time:{Time.realtimeSinceStartup - startTime}");

    position.Dispose();
    velocity.Dispose();
}
```
不使用Burst，运行时间约为80ms-90ms
![Alt text](assets/unity_game/image-1.png)

```
[BurstCompile]
public struct VelocityJob : IJobFor
{
    //不需要写入的可以标记为只读数据，优化效率
    [ReadOnly]
    public NativeArray<Vector3> velocity;
    public NativeArray<Vector3> position;
    public float deltaTime;

    public void Execute(int i)
    {
        position[i] = position[i] + velocity[i] * deltaTime;
    }
}
```
添加Burst，只需要在继承IJob的结构体或对应方法加上标签[BurstCompile]
再次执行程序，运行时间约为9s-10ms，快了近10倍
![Alt text](assets/unity_game/image-2.png)