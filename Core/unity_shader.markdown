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
