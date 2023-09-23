- [Unity Game（Unity开发）](#unity-gameunity开发)
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


# Unity Game（Unity开发）
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
Unity提供的多线程技术，可以将任务分给多个线程并行执行，以此提高运行效率</br>
[Unity学习—JobSystem](https://zhuanlan.zhihu.com/p/148160780)

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
一种托管值类型。包含指向非托管分配的指针。当与JobSystem一起使用时，NativeContainer允许Job访问与主线程共享的数据，而非数据副本
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
参数2是分配类型(根据Job的执行时长决定)</br>
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
    JobHandle scheduleJobDependency = new JobHandle();
    //运行在单个工作线程(参数1为Execute总执行次数 参数2为需要等待的依赖任务)
    JobHandle scheduleJobHandle = job.Schedule(position.Length, scheduleJobDependency);
    //并行在多个工作线程(参数1为Execute总执行次数 参数2为单批次执行次数 参数3为需要等待的依赖任务)
    JobHandle scheduleParallelJobHandle = job.ScheduleParallel(position.Length, 64, scheduleJobHandle);
    //等待并行任务结束
    scheduleParallelJobHandle.Complete();
    
    Debug.Log(job.position[0]);

    position.Dispose();
    velocity.Dispose();
}
```