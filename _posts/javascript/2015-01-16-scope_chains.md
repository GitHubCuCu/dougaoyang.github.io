---
layout: post
title: JavaScript作用域链分析
category: JavaScript
description: 对js作用域链的解析，和如何改变作用域链
keywords: JavaScript,作用域链,变量
---

##1.前言
作用域概念是理解 JavaScript 的关键，不仅从性能的角度，而且从功能的角度。作用域对 JavaScript 有许多影响，从确定哪些变量可以被函数访问，到确定 this 的值。

##2.变量
在 JavaScript 里,对象/函数都是变量。window对象是全局对象，全局变量是全局对象的属性。每个变量内部又有自己的变量，就是局部变量。

```JavaScript
var age = 22; // 全局变量
function t(){
    var num = 88; // 局部变量
}
```

每个变量都有自己的作用域，上面，`num`变量它的作用域为函数t，在t函数内部可以获取他，在函数外部则不行。基本上大多数的编程语言都遵循这种规律，然而JavaScript却有一个独特的地方，就是在变量内部可以访问到外部的变量。

以函数为例，在运行函数时，函数收到它运行时的环境影响，不同的外部环境可能产生的结果会不一样。

```JavaScript
var age = 22;
var num = 99;
function t () {
    var num = 88;
    var str= 'hello';
    alert(age); // 22
    alert(str); // hello
    alert(num); // 88
}
t();
```

来分析一下上面的例子，首先定义了全局变量age、num和函数t。在函数t中定义的局部变量num和str。  
执行函数t时，局部变量里面没有age变量，于是就去函数t的外部去寻找，找到全局变量age，返回22；  
局部变量里面有str和num变量，于是直接返回hello和88，不继续向外部寻找。

![作用域分析][1]

##3.词法分析

一个栗子：

```JavaScript
var str = 'global';
function t() {
    alert(str); // undefined
    var str = 'local';
    alert(str); // local

}
t();
```

产生上述结果的原因是JavaScript并不是一句一句顺序执行的，先进行词法分析(预编译)。当函数运行时，它的作用域链被初始化，创建一个激活对象（Activation Object），以下简称AO。此激活对象作为函数执行期的一个可变对象，包含访问所有局部变量，命名参数，参数集合，和 this的接口。

**js的执行顺序**  

*词法分析阶段*

1. 分析参数
2. 分析变量声明
3. 分析函数声明

说明：
　1.先把接收到的参数放到AO上  
　2.分析变量声明  
　　a: var xx = yy;  
　　做法:先分析var xx，声明一个xx属性在AO上，xx变量此时,没有赋值,值是undefined，但如果已经有xx,则不对xx操作。  
　　b:function t() {}    
　　做法:直接声明t属性，且内容是函数体  

*执行语句阶段*

举个栗子：

```JavaScript
function t(age) {
    alert(age);  // 99
    var age = 12;
    alert(age);  // 12
}
t(99);
```

分析一下：
1. 执行t函数，产生t的AO ==> t:AO:{}
2. 词法分析形参得到     ==> t:AO:{age:undefined} 
3. 实参赋值 age属性     ==> t:AO:{age:99}         
4. 修改age的值          ==> t:AO:{age:12}         

继续举个大栗子：

```JavaScript
function a(b) {
    alert(b);  // function b(){}
    function b(){
        alert (b); // function b(){}
    }
    b();
}
a(1);
```

分析一下：
1. 执行a函数，产生a的AO ==> a:AO:{}
2. 词法分析形参得到           ==> a:AO:{b:undefined}   
3. 实参赋值 b属性             ==> a:AO:{b:1}
4. 函数声明 b属性             ==> a:AO:{b:function}
5. 执行b函数，产生b的AO ==> b:AO:{}
6. 由于在b的AO中没有b属性，向b函数外部寻找到a的AO，找到b属性，为function。AO.b --->{}--->a:AO


有上面的例子可以看出，函数的作用域是和外部环境有着密切的关联，是一级一级通过激活对象向外部关联，一直到window对象为止，这样就形成了函数的作用域链。

##4.总结
JavaScript并不是一句一句顺序执行的，会先进行词法分析，产出激活对象。在js执行时，变量的访问会沿着激活对象向外部延伸从而产生一条作用域链。当作用域链被销毁时，激活对象也一同销毁。



[1]: /images/20150116124731.jpg "作用域分析"

