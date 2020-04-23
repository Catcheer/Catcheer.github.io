
---
title: Object.assign
date: 2020-04-23 14:25:38
tags:
---


> Object.assign() 方法用于将所有可枚举属性的值从一个或多个源对象复制到目标对象。它将返回目标对象。


注意限定条件  <b style="color:#DB4D46">可枚举属性</b>


本来是在MDN上浏览Object.create 其中有几个例子用到了js继承

```javascript
function SuperClass(){
}
function OtherSuperClass(){
}
function MyClass() {
  SuperClass.call(this);
  OtherSuperClass.call(this);
}
function MyClass() {
  SuperClass.call(this);
  OtherSuperClass.call(this);
}

// 继承一个类
MyClass.prototype = Object.create(SuperClass.prototype);
// 混合其它
Object.assign(MyClass.prototype, OtherSuperClass.prototype);
// 重新指定constructor
MyClass.prototype.constructor = MyClass;

MyClass.prototype.myMethod = function() {
  // do a thing
};

const me= new MyClass()
console.log(me instanceof MyClass) // 1 true
console.log(me instanceof SuperClass) //2 true
console.log(me instanceof OtherSuperClass) //3 false
```

因为me实例的原型对象(包括原型链上的)分别可以找到constructor:MyClass,constructor:SuperClass所以1,2为true.怀着好奇心 怎样修改代码可以实现3为true呐,然后看到了这句
```javascript
// 重新指定constructor
MyClass.prototype.constructor = MyClass;
```
constructor重新指向了MyClass  大胆猜想如果把这行删除,而Object.assign(MyClass.prototype, OtherSuperClass.prototype);MyClass.prototype与OtherSuperClass.prototype进行了混合,混合后的MyClass.prototype.constructor不应该是OtherSuperClass嘛 ,于是索性把代码改成了

```javascript
function SuperClass(){
}
function OtherSuperClass(){
}
function MyClass() {
  SuperClass.call(this);
  OtherSuperClass.call(this);
}
function MyClass() {
  SuperClass.call(this);
  OtherSuperClass.call(this);
}

// 继承一个类
MyClass.prototype = Object.create(SuperClass.prototype);
// 混合其它
Object.assign(MyClass.prototype, OtherSuperClass.prototype);


MyClass.prototype.myMethod = function() {
  // do a thing
};
const me= new MyClass()
console.log(me instanceof OtherSuperClass) 

```

结果 赤裸裸的打印出了false.
又怀疑Object.assign不是源对象属性重写目标对象属性?? 赶紧打印
```javascript
console.log(Object.assign({a:1},{a:2})) // {a:2}
```

可以看到是源对象属性覆盖目标对象相同的属性,可是用在protoype怎么就不生效呐.想了一下 是不是跟对象的所有自身属性的描述有关,于是打印

```javascript
console.log(Object.getOwnPropertyDescriptors(OtherSuperClass.prototype))
```
可以看到constructor属性的enumerable: false 至此基本确信使用Object.assign时enumerable: false的属性不会被复制到目标对象.
为了验证,又打印了一下代码

```javascript
console.log(Object.assign({a:1},Object.defineProperty({},'a',{enumerable: false,value:2}))) 

```

结果是{a:1},证实了自己的猜想.然后MDN上搜索Object.assign 看到了开篇的那句 Object.assign() 方法用于将所有可枚举属性的值从一个或多个源对象复制到目标对象。它将返回目标对象。