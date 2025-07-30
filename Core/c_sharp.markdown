- [C Sharp(C#)](#c-sharpc)
  - [operator sign（运算符重载）](#operator-sign运算符重载)
    - [注意](#注意)
  - [operator class（类型转化）](#operator-class类型转化)
    - [explicit（显示转化）](#explicit显示转化)
    - [implicit（隐式转化）](#implicit隐式转化)
    - [注意](#注意-1)
  - [LINQ语法](#linq语法)


# C Sharp(C#)
## operator sign（运算符重载）
在类里声明运算符重载，实现运算符直接对类实例进行计算
运算符：
"+" "-" "*" "/"等
语法：
public static Class（对应类）operator sign（+/-/*//）（Class（对应类）p1（参数1）, Class（任意）p2（参数2））
例：
![alt text](assets/c_sharp/image.png)
![alt text](assets/c_sharp/image-1.png)
### 注意
其中一个参数必须是当前声明的类型
参数只能有2个

## operator class（类型转化）
### explicit（显示转化）
语法：
public static explicit operator Class（返回的转化类型 任意类）（Class（当前类）p（参数））
例：
![alt text](assets/c_sharp/image-2.png)
![alt text](assets/c_sharp/image-3.png)
public static explicit operator Class（返回的转化类型 当前类）（Class（任意类）p（参数））
例：
![alt text](assets/c_sharp/image-4.png)
![alt text](assets/c_sharp/image-5.png)
### implicit（隐式转化）
public static implicit operator Class（返回的转化类型 任意类）（Class（当前类）p（参数））
例：
![alt text](assets/c_sharp/image-6.png)
![alt text](assets/c_sharp/image-7.png)
public static implicit operator Class（返回的转化类型 当前类）（Class（任意类）p（参数））
例：
![alt text](assets/c_sharp/image-8.png)
![alt text](assets/c_sharp/image-9.png)
### 注意
参数和返回值其中一个必须是当前声明的类型
参数只能有一个

## LINQ语法
用类SQL语法对C#集合执行操作
```C#
List<string> linqList = new List<string>()
{
    "2", "4", "5", "3", "1"
};

//过滤操作，将结果小于4的值加入数组，最后输出结果为"2" "3" "1"
List<string> whereList = linqList.Where(value => int.Parse(value) < 4).ToList();
foreach (var value in whereList)
{
    Debug.Log(value);
}

//修改操作，将原生值改为别的值，将字符类型转换为整型并减1，最后输出结果为1 3 4 2 0
List<int> selectList = linqList.Select(value => int.Parse(value)-1).ToList();
foreach (var value in selectList)
{
    Debug.Log(value);
}

//排序，根据给定的返回值排序（数字是从小到大，文本则按字符的ascii顺序），最后输出结果为"1" "2" "3" "4" "5"
List<string> orderList = linqList.OrderBy(value => int.Parse(value)).ToList();
foreach (var value in orderList)
{
    Debug.Log(value);
}

//链式写法，可以将复合操作结合，最后再输出最终结果0 1 2
linqList.Where(value => int.Parse(value) < 4).Select(value => int.Parse(value) - 1).OrderBy(value => value).ToList();
foreach (var value in linqList)
{
    Debug.Log(value);
}

```