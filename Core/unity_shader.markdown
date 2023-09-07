# Unity Shader（着色器）

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
![Alt text](assets/unity_shader/image-6.png)

声明一个名为"CSMain"的核函数<br/>
只有声明后该函数才会被GPU执行<br/>
一个CS文件内至少需要<font color=#FF0000>一个</font>核函数

#### 声明传入参数
![Alt text](assets/unity_shader/image-7.png)
声明一个名为"Result"的读写纹理参数<br/>
这个参数通过外部（csharp脚本）赋值，并在CSMain函数内进行计算<br/>

#### 声明线程分配
![Alt text](assets/unity_shader/image-8.png)

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
![Alt text](assets/unity_shader/image-10.png)

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
    }
}
```
![Alt text](assets/unity_shader/image-18.png)

创建需要计算的数组
![Alt text](assets/unity_shader/image-13.png)
根据数组长度，创建输入输出的ComputeBuffer。第一个参数是数组长度，第二个是组内单个元素的字节占用。这边单个元素是int类型，也就是4字节<br/>
然后将上面声明的int数组设置为输入数据<br/>
[关于ComputeBuff的创建与其对应的ComputeShader数据类型](https://docs.unity3d.com/ScriptReference/ComputeBufferType.html)
![Alt text](assets/unity_shader/image-19.png)
设置ComputeShader内的输入输出参数，然后执行函数
![Alt text](assets/unity_shader/image-20.png)

获取ComputeShader计算后的输出参数，将结果存在数组中，打印输出
![Alt text](assets/unity_shader/image-21.png)
![Alt text](assets/unity_shader/image-22.png)