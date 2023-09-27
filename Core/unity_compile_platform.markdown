- [Unity Compile \& Platform(Unity编译与平台)](#unity-compile--platformunity编译与平台)
  - [.Net](#net)
    - [.Net IDE(集成开发环境)](#net-ide集成开发环境)
    - [.Net SDK(软件开发工具包)](#net-sdk软件开发工具包)
    - [.Net Runtime(程序运行环境)](#net-runtime程序运行环境)
    - [.Net CLI(Command-Line Interface 命令行接口工具)](#net-clicommand-line-interface-命令行接口工具)
    - [CLR(Common Language Runtime 公共语言运行时)](#clrcommon-language-runtime-公共语言运行时)
      - [不同的CLR](#不同的clr)
      - [CLR的基本功能](#clr的基本功能)
    - [BCL(Base Class Libaary 基础类库/运行时库)](#bclbase-class-libaary-基础类库运行时库)
    - [CTS(Common Type System 公共类型系统)](#ctscommon-type-system-公共类型系统)
    - [CLS(Common Language Specification 公共语言规范)](#clscommon-language-specification-公共语言规范)
    - [MSIL/CIL(Microsoft Intermediate Language/Common Intermediate Language 中间语言)](#msilcilmicrosoft-intermediate-languagecommon-intermediate-language-中间语言)
    - [元数据(Metadata)](#元数据metadata)
    - [程序集(Assembly)](#程序集assembly)
    - [本机代码(Native Code)](#本机代码native-code)
    - [JIT(Just In Time 即时编译器)](#jitjust-in-time-即时编译器)
    - [AOT(Ahead Of Time 预编译器)](#aotahead-of-time-预编译器)
    - [.Net程序执行流程](#net程序执行流程)

# Unity Compile & Platform(Unity编译与平台)

## .Net
[.NET相关概念](https://blog.csdn.net/NRatel/article/details/127148390)</br>
[通俗易懂，什么是.NET?什么是.NET Framework？什么是.NET Core?](https://cloud.tencent.com/developer/inventory/28795/article/1459796)</br>
微软提供的一个跨语言、跨平台的开发者平台</br>
跨语言:所有面向.Net平台的编程语言（可编译为IL,如C#、VB、C++），可以互相交互</br>
跨平台:程序可以运行在任何实现了.Net框架的平台上(Window、Android)</br>
### .Net IDE(集成开发环境)
包含了.Net SDK(软件开发工具包) + 代码编辑器 + 调试器 + 图形用户界面
### .Net SDK(软件开发工具包)
包含了.Net Runtime(程序运行环境) + .Net CLI(命令行接口工具) + Compiler(语言编译器) + Build Engine(构建引擎)
### .Net Runtime(程序运行环境)
包含了CLR(公共语言运行时 程序运行的虚拟机) + BCL(基础类库 程序运行时需要依赖的库)
### .Net CLI(Command-Line Interface 命令行接口工具)
调用Compiler(语言编译器)和Build Engine(构建引擎)的相关接口
### CLR(Common Language Runtime 公共语言运行时)
不同语言（面向.Net平台的语言）通用的运行时环境</br>
也被称为"托管"运行时，他使用垃圾回收器进行内存管理</br>
可以使用JIT编译器快速生成和编译代码</br>
#### 不同的CLR
.Net Framework的CLR是.Net Framework CLR(仅支持Windows桌面)</br>
.Net 5和.Net Core的CLR是Core CLR(跨平台)</br>
Mono的CLR是Mono CLR(跨平台)</br>
#### CLR的基本功能
1.可使用其他语言开发的组件</br>
2.语言功能，使用面向对象编程的继承、接口、重载</br>
3.垃圾回收</br>
4.使用委托取代函数指针
### BCL(Base Class Libaary 基础类库/运行时库)
应用程序在运行时必须依赖的库，也被称为框架库，核心.Net库。当新建项目后基础类库就已经被引用了。基础类库包含基础数据类型、文件操作、集合、自定义属性、格式设置、安全属性、I/O流、字符串操作
### CTS(Common Type System 公共类型系统)
CTS是跨语言互相操作的条件。定义了各种开发语言必须遵守的规则</br>
定义内容:</br>
1.数据类型:引用和值类型</br>
2.类型类别:类、结构、枚举、接口、委托</br>
3.原则和特征:特性、可访问性、名称、基类、实现的接口、成员、继承、重写、隐藏成员等</br>
### CLS(Common Language Specification 公共语言规范)
CLS是CTS的子集</br>
CLS是语言可与其他语言完成跨语言互操作的条件(不同语言进行交互的最低要求)</br>
规范内容:类型转换、数组、接口、枚举、成员访问性、泛型、构造函数、属性、特性</br>
CLS规范只限定于公开可以被外部访问的成员(Public、Protected)，私有Private与内部Internal成员没有该限定
### MSIL/CIL(Microsoft Intermediate Language/Common Intermediate Language 中间语言)
IL又被称为IL或CIL(Common IL)</br>
IL是一种可以被对应编译器转化为本机代码的中间语言</br>
受到任何操作系统的支持</br>
### 元数据(Metadata)
元数据是一个数据表的集合。数据表描述了模块定义了什么(类型的定义)及引用了什么(代码引用的成员)</br>
### 程序集(Assembly)
程序集是.dll或.exe文件</br>
程序集中存在一个清单，也就是元数据集合，描述了构成程序集的所有文件名称</br>
### 本机代码(Native Code)
本机代码就是最终运行在各平台CPU上的机器码
### JIT(Just In Time 即时编译器)
在应用程序执行时进行，将执行函数的IL转化为对应平台处理的机器码，并缓存。下次再执行对应函数时则直接使用转化后的机器码
### AOT(Ahead Of Time 预编译器)
在应用程序执行前(程序安装时)就将所有IL翻译成对应平台处理的机器码

### .Net程序执行流程
1.对应语言的编译器(如c#编译器)会将开发代码(如c#脚本)编译为MSIL(中间语言)</br>
2.接着将MSIL和元数据转化为托管模块</br>
3.编译器会将托管模块和清单转化为程序集(其实就是含有清单的托管模块)</br>
4.对应编译器(JIT编译器/AOT编译器)将中间语言编译为当前平台的机器码(本机代码)，编译方式分为JIT编译(运行时编译)和AOT(预编译)</br>
5.运行本地代码</br>
