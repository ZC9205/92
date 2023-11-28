- [Unity Shader（着色器）](#unity-shader着色器)
  - [预处理器指令](#预处理器指令)
    - [pragma](#pragma)
  - [着色器变体](#着色器变体)
    - [使用场景](#使用场景)
    - [创建变体（关键字声明）](#创建变体关键字声明)
      - [#pragma multi\_compile](#pragma-multi_compile)
      - [#pragma multi\_compile\_local](#pragma-multi_compile_local)
      - [#pragma shader\_feature](#pragma-shader_feature)
      - [#pragma shader\_feature\_local](#pragma-shader_feature_local)
    - [启用变体](#启用变体)
      - [Shader.EnableKeyword](#shaderenablekeyword)
      - [Shader.DisableKeyword](#shaderdisablekeyword)
      - [Material.EnableKeyword](#materialenablekeyword)
      - [Material.DisableKeyword](#materialdisablekeyword)
  - [Stencil Buff（模板缓冲）](#stencil-buff模板缓冲)
    - [渲染阶段](#渲染阶段)
    - [属性值](#属性值)
      - [Ref（对比值）](#ref对比值)
      - [Comp (对比方法)](#comp-对比方法)
      - [Pass (通过模板测试后的操作)](#pass-通过模板测试后的操作)
      - [Fail (未通过模板测试后的操作)](#fail-未通过模板测试后的操作)
      - [ZFail (通过模板测试但未通过深度测试后的操作)](#zfail-通过模板测试但未通过深度测试后的操作)
      - [ReadMask (读取掩码)](#readmask-读取掩码)
      - [WriteMask (写入掩码)](#writemask-写入掩码)
  - [Compute Shader(计算着色器)](#compute-shader计算着色器)
    - [Thread Group \& Thread(线程组与线程)](#thread-group--thread线程组与线程)
      - [Thread Group（线程组）](#thread-group线程组)
      - [Thread（线程）](#thread线程)
      - [SV\_GroupID : uint3（线程组id）](#sv_groupid--uint3线程组id)
      - [SV\_GroupThreadID : uint3（线程组内线程id）](#sv_groupthreadid--uint3线程组内线程id)
      - [SV\_DispatchThreadID : uint3（所有线程组范围的线程id）](#sv_dispatchthreadid--uint3所有线程组范围的线程id)
      - [SV\_GroupIndex : uint（线程组内线程的下标）](#sv_groupindex--uint线程组内线程的下标)
    - [Compute Shader纹理渲染](#compute-shader纹理渲染)
      - [声明核函数](#声明核函数)
      - [声明传入参数](#声明传入参数)
      - [声明线程分配](#声明线程分配)
      - [核函数逻辑](#核函数逻辑)
      - [外部脚本逻辑](#外部脚本逻辑)
    - [Compute Shader GPU计算](#compute-shader-gpu计算)
      - [声明传入传出参数](#声明传入传出参数)
      - [核函数逻辑](#核函数逻辑-1)
      - [外部脚本逻辑](#外部脚本逻辑-1)


# Unity Shader（着色器）


## 预处理器指令
### pragma 
pragma是一种预处理器指令，将其放置在Shader语言的代码块内（如:CGPROGRAM..ENDCG）使用。常见的有
pragma vertex name 声明顶点着色器名称
pragma fragment name 声明片元着色器名称

## 着色器变体
在一份着色器文件内声明关键词，Unity会根据关键词的数量生成对应数量的着色器变体

### 使用场景
很多时候，同一份着色器代码主体可以作用在很多地方但细节部分需要调整
在gpu内使用if..else会有一定量消耗
复制多份着色器文件来进行切换不利于维护（一旦主体需要修改，就得同时修改多份文件）
这时候可以使用着色器变体来进行替代

### 创建变体（关键字声明）
#### #pragma multi_compile
声明全局关键字。可以一次声明多个，每声明一个就会产生一个shader变体
由于Unity的全局关键字有上限（目前是384，并且有部分Unity自己使用了），不建议声明过多
和shader_feature不同，声明的变体不论有没有使用到都会包含在构建包内
例：
![Alt text](assets/unity_shader/image-24.png)
![Alt text](assets/unity_shader/image-25.png)
以上声明会产生3个变体
默认变体 不包含A、B部分
A变体 包含默认和A部分
B变体 包含默认和B部分
#### #pragma multi_compile_local 
声明本地关键字。和multi_compile的区别是，通过multi_compile_local声明的关键字占用的是该shader的本地关键字计数
#### #pragma shader_feature
和multi_compile一样声明全局关键字。区别在于shader_feature声明后但没用到的关键字会从构建包内剔除
例：
![Alt text](assets/unity_shader/image-26.png)
![Alt text](assets/unity_shader/image-27.png)
声明了关键字D、E，但实际只使用了D，因此关键字E在打包时会被剔除。如果在代码内启用E会发现命令失效
#### #pragma shader_feature_local
声明本地关键字。和shader_feature的区别是，通过shader_feature声明的关键字占用的是该shader的本地关键字计数

### 启用变体
#### Shader.EnableKeyword
![Alt text](assets/unity_shader/image-28.png)
启用全局关键字变体。开启后游戏内使用该关键字的着色器都会被切换为该变体。只对声明为全局的关键字有效（同时启用全局关键字和局部关键字时，最终作用的是局部关键字？）
#### Shader.DisableKeyword
![Alt text](assets/unity_shader/image-29.png)
关闭全局关键字变体。关闭后游戏内使用该关键字的着色器都会被切换为默认变体。只对声明为全局的关键字有效
#### Material.EnableKeyword
![Alt text](assets/unity_shader/image-30.png)
启用局部关键字变体。开启后对应材质的着色器会被切换为该变体。对局部和全局关键字都有效（但是用该函数开启的话全局关键字似乎会被视为局部关键字，需要用Material.DisableKeyword才能切回默认变体）
#### Material.DisableKeyword
![Alt text](assets/unity_shader/image-31.png)
关闭局部关键字变体。关闭后对应材质的着色器会被切换为默认变体。对局部关键字和用Material.EnableKeyword开启的变体有效（即便是全局关键字也会被视为局部关键字？）

## Stencil Buff（模板缓冲）

### 渲染阶段
模板缓冲执行在逐片元阶段之后，深度测试(ZTest)之前。如果模板测试未通过，就会直接舍弃该片元的颜色值，不会再继续之后的测试

### 属性值
![Alt text](assets/unity_shader/image-1.png)
![Alt text](assets/unity_shader/image.png)

#### Ref（对比值）
当前材质的对比值。用来和模板缓冲内现存的模板值做对比

#### Comp (对比方法)
当前对比值ref与模板缓冲内的模板值对比的方法。可以参考Unity提供的枚举CompareFunction

```c#
public enum CompareFunction
  {
    /// <summary>
    ///   <para>Depth or stencil test is disabled.</para>
    /// </summary>
    Disabled,
    /// <summary>
    ///   <para>Never pass depth or stencil test.</para>
    /// </summary>
    Never,
    /// <summary>
    ///   <para>Pass depth or stencil test when new value is less than old one.</para>
    /// </summary>
    Less,
    /// <summary>
    ///   <para>Pass depth or stencil test when values are equal.</para>
    /// </summary>
    Equal,
    /// <summary>
    ///   <para>Pass depth or stencil test when new value is less or equal than old one.</para>
    /// </summary>
    LessEqual,
    /// <summary>
    ///   <para>Pass depth or stencil test when new value is greater than old one.</para>
    /// </summary>
    Greater,
    /// <summary>
    ///   <para>Pass depth or stencil test when values are different.</para>
    /// </summary>
    NotEqual,
    /// <summary>
    ///   <para>Pass depth or stencil test when new value is greater or equal than old one.</para>
    /// </summary>
    GreaterEqual,
    /// <summary>
    ///   <para>Always pass depth or stencil test.</para>
    /// </summary>
    Always,
  }
```

#### Pass (通过模板测试后的操作)
通过模板测试后对当前模板缓冲值进行的操作。可以参考Unity提供的枚举StencilOp

```c#
public enum StencilOp
  {
    /// <summary>
    ///   <para>Keeps the current stencil value.</para>
    /// </summary>
    Keep,
    /// <summary>
    ///   <para>Sets the stencil buffer value to zero.</para>
    /// </summary>
    Zero,
    /// <summary>
    ///   <para>Replace the stencil buffer value with reference value (specified in the shader).</para>
    /// </summary>
    Replace,
    /// <summary>
    ///   <para>Increments the current stencil buffer value. Clamps to the maximum representable unsigned value.</para>
    /// </summary>
    IncrementSaturate,
    /// <summary>
    ///   <para>Decrements the current stencil buffer value. Clamps to 0.</para>
    /// </summary>
    DecrementSaturate,
    /// <summary>
    ///   <para>Bitwise inverts the current stencil buffer value.</para>
    /// </summary>
    Invert,
    /// <summary>
    ///   <para>Increments the current stencil buffer value. Wraps stencil buffer value to zero when incrementing the maximum representable unsigned value.</para>
    /// </summary>
    IncrementWrap,
    /// <summary>
    ///   <para>Decrements the current stencil buffer value. Wraps stencil buffer value to the maximum representable unsigned value when decrementing a stencil buffer value of zero.</para>
    /// </summary>
    DecrementWrap,
  }
```

#### Fail (未通过模板测试后的操作)
未通过模板测试后对当前模板缓冲值进行的操作。未通过后虽然该片元会被舍弃，但是可以通过设置StencilOp来修改当前的模板缓冲值。可以参考Unity提供的枚举StencilOp

#### ZFail (通过模板测试但未通过深度测试后的操作)
虽然通过了模板测试，但由于之后的深度测试未通过，导致该片元会被舍弃。但可以通过设置StencilOp来修改当前的模板缓冲值。可以参考Unity提供的枚举StencilOp

#### ReadMask (读取掩码)
一个0-255的值。当执行Comp操作时，需要读取当前对比值Ref&ReadMask，模板缓冲值Stencil&ReadMask，最后再带入Comp进行运算
eg. Ref=255 Stencil=3 ReadMask=1 Comp=3(Equal)
Ref == Stencil&ReadMask = true(通过测试)

#### WriteMask (写入掩码)
一个0-255的值。当执行StencilOp操作时，读取对应值(参考值Ref/模板缓冲值Stencil)Value&WriteMask,再将结果&WriteMask写入模板缓冲中
eg. Ref=3 WriteMask=1 StencilOp=2(Replace)
Ref&WriteMask = 1 最终模板缓冲值=1
eg. Stencil=4 WriteMask=7 StencilOp=4(DecrementSaturate)
Stencil&WriteMask - 1&WriteMask = 3&WriteMask 最终模板缓冲值=3


## Compute Shader(计算着色器)
[参考链接](https://zhuanlan.zhihu.com/p/368307575#RWStructuredBuffer)<br/>
CS（Compute Shader）是一种可编程着色器。独立于渲染管线之外，可对GPU资源进行存取操作。可以通过CS访问GPU并执行一些操作（和渲染无关的复杂运算，纹理处理等）

### Thread Group & Thread(线程组与线程)
GPU编程会先分配线程组，再分配每个线程组的线程数量，核函数则运行在单个线程上

#### Thread Group（线程组）
在执行CS时，使用Dispatch(gX,gY,gZ)来分配核函数的线程组<br/>
例:<br/>
Dispatch(gX,gY,gZ)<br/>
会为该函数分配gX * gY * gZ个线程组<br/>

#### Thread（线程）
[numthreads(tX,tY,tZ)]定义在CS的核函数上，定义为<font color=#FF0000>每个线程组</font>中使用的线程范围<br/>
例:<br/>
Dispatch(2,2,1) 线程组<br/>
numthreads(3,2,1) 线程<br/>
会为该函数分配4个线程组，每个线程组包含6个线程，总计2 * 2 * 1 * 3 * 2 * 1 = 24个线程<br/>
SV_GroupThreadID(线程组内线程id)<font color=#FF0000>按顺序</font>分别为(0,0,0),(1,0,0),(2,0,0),(0,1,0),(1,1,0),(2,1,0)<br/>
SV_DispatchThreadID(包含所有线程组范围的线程id)从(0,0,0) - (5,3,0)<br/>
SV_GroupIndex(当前线程在线程组内下标)从0-5

#### SV_GroupID : uint3（线程组id）
例:<br/>
线程组(2,2,1)<br/>
SV_GroupID (0,0,0),(1,0,0),(0,1,0),(1,1,0)

#### SV_GroupThreadID : uint3（线程组内线程id）
例:<br/>
线程(3,2,1)<br/>
SV_GroupThreadID (0,0,0),(1,0,0),(2,0,0),(0,1,0),(1,1,0),(2,1,0)

#### SV_DispatchThreadID : uint3（所有线程组范围的线程id）
例:<br/>
线程组(gX,gY,gZ) (2,2,1)<br/>
线程(tX,tY,tZ) (3,2,1)<br/>
(0,0,0) - (gX * tX - 1, gY * tY - 1, gZ * tZ - 1)<br/>
(0,0,0) - (5,3,0)

#### SV_GroupIndex : uint（线程组内线程的下标）
例:<br/>
线程(3,2,1)<br/>
0-5

### Compute Shader纹理渲染
```
#pragma kernel CSMain

RWTexture2D<float4> Result;

[numthreads(64,64,1)]
void CSMain (uint3 id : SV_DispatchThreadID, uint3 gid : SV_GroupID, uint pid : SV_GroupIndex, uint3 gtid : SV_GroupThreadID)
{
    Result[id.xy] = float4(id.x & id.y, (id.x & 15) / 15.0, (id.y & 15) / 15.0, 0.0);
}
```

#### 声明核函数
![Alt text](assets/unity_shader/image-6.png)<br/>
声明一个名为"CSMain"的核函数<br/>
只有声明后该函数才会被GPU执行<br/>
一个CS文件内至少需要<font color=#FF0000>一个</font>核函数

#### 声明传入参数
![Alt text](assets/unity_shader/image-7.png)
声明一个名为"Result"的读写纹理参数<br/>
这个参数通过外部（csharp脚本）赋值，并在CSMain函数内进行计算<br/>

#### 声明线程分配
![Alt text](assets/unity_shader/image-8.png)<br/>
单一线程组内的线程分配

#### 核函数逻辑
![Alt text](assets/unity_shader/image-9.png)
核函数是并行运作，所以执行时是乱序的<br/>
核函数参数,可以获取到SV_DispatchThreadID，SV_GroupID，SV_GroupIndex，SV_GroupThreadID这4个参数（顺序自定义，如果不需要对应的参数可以去除）<br/>
该函数通过获取对应线程在线程组内的xy值来填充对应纹理的像素值，每个函数会填充1个像素值，最后填满

#### 外部脚本逻辑
```c#
public class ComputedScript0 : MonoBehaviour
{
    public ComputeShader computeShader;
    private RenderTexture m_RenderTexture;
    private int m_KernelIndex;
    
    void Start()
    {
        m_RenderTexture = new RenderTexture(256,256,1);
        m_RenderTexture.enableRandomWrite = true;
        m_RenderTexture.Create();
        
        var mat = GetComponent<MeshRenderer>().material;
        mat.SetTexture("_MainTexture", m_RenderTexture);
        
        m_KernelIndex = computeShader.FindKernel("CSMain");
    }
    
    void Update()
    {
        computeShader.SetTexture(m_KernelIndex, "Result", m_RenderTexture);
        computeShader.Dispatch(m_KernelIndex, 4, 4, 1);
    }
}
```
![Alt text](assets/unity_shader/image-2.png)
首先按需求大小创建纹理
![Alt text](assets/unity_shader/image-3.png)
然后将创建出来的纹理设置为想要显示物体的材质纹理
![Alt text](assets/unity_shader/image-5.png)
找到对应核函数的index，将纹理作为参数传入ComputeShader。然后设置线程组为(4,4,1)，执行核函数<br/>
线程设置为(64,64,1)<br/>
线程组设置为(4,4,1)<br/>
纹理大小为(256,256,1)<br/>
最后为每个线程的核函数执行一次并填充一个像素

### Compute Shader GPU计算
```
#pragma kernel CSMain

StructuredBuffer<int> InputBuffer;
RWStructuredBuffer<int> OutputBuffer;

[numthreads(4,4,1)]
void CSMain (uint3 groupId : SV_GroupID, uint groupIndex : SV_GroupIndex)
{
    int threadNum = 4 * 4 * 1;
    int curIndex = groupId.z * (3 * 2) * threadNum + groupId.y * 3 * threadNum + groupId.x * threadNum + groupIndex;
    OutputBuffer[curIndex] = InputBuffer[curIndex] * 2;
}
```

#### 声明传入传出参数
![Alt text](assets/unity_shader/image-10.png)<br/>
声明2个参数 InputBuffer作为输入 OutputBuffer作为输出<br/>
这两个都是包含int类型的数组(也可以自定义struct类型)
#### 核函数逻辑
![Alt text](assets/unity_shader/image-17.png)
算出每个线程组拥有的线程数<br/>
通过SV_GroupID（当前线程组id）SV_GroupIndex（当前线程在线程组对应下标）算出当前线程对应所有线程组范围的下标<br/>
获取输入参数值，写入输出参数结果
#### 外部脚本逻辑
```c#
public class ComputedScript1 : MonoBehaviour
{
    public ComputeShader computeShader;

    void Start()
    {
        var arr = new int[100];
        for (int i = 0; i < arr.Length; i++)
        {
            arr[i] = i;
        }
        var inputData = new ComputeBuffer(arr.Length, 4);
        inputData.SetData(arr);
        var outputData = new ComputeBuffer(arr.Length, 4);
        var kernelIndex = computeShader.FindKernel("CSMain");
        computeShader.SetBuffer(kernelIndex, "InputBuffer", inputData);
        computeShader.SetBuffer(kernelIndex, "OutputBuffer", outputData);
        computeShader.Dispatch(kernelIndex, 3, 2, 1);
        outputData.GetData(arr);
        for (int i = 0; i < arr.Length; i++)
        {
            Debug.Log(arr[i]);
        }
        inputData.Release();
        outputData.Release();
    }
}
```
![Alt text](assets/unity_shader/image-18.png)<br/>
创建需要计算的数组
![Alt text](assets/unity_shader/image-13.png)
根据数组长度，创建输入输出的ComputeBuffer。第一个参数是数组长度，第二个是组内单个元素的字节占用。这边单个元素是int类型，也就是4字节<br/>
然后将上面声明的int数组设置为输入数据<br/>
[关于ComputeBuff的创建与其对应的ComputeShader数据类型](https://docs.unity3d.com/ScriptReference/ComputeBufferType.html)
![Alt text](assets/unity_shader/image-19.png)
设置ComputeShader内的输入输出参数，然后执行函数
![Alt text](assets/unity_shader/image-20.png)
![Alt text](assets/unity_shader/image-21.png)
![Alt text](assets/unity_shader/image-22.png)
获取ComputeShader计算后的输出参数，将结果存在数组中，打印输出
![Alt text](assets/unity_shader/image-23.png)<br/>
最后记得释放不需要的ComputeBuffer