- [C Sharp(C#)](#c-sharpc)
  - [operator sign（运算符重载）](#operator-sign运算符重载)
    - [注意](#注意)
  - [operator class（类型转化）](#operator-class类型转化)
    - [explicit（显示转化）](#explicit显示转化)
    - [implicit（隐式转化）](#implicit隐式转化)
    - [注意](#注意-1)


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