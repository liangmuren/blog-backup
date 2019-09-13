---
title: JS继承实现方式整理
tags: '前端, JavaScript'
date: 2019-04-08 20:38:54
---


> 整理源于
>
> 《JavaScript高级程序设计（第三版）》
>
> [《ECMAScript 6入门》](http://es6.ruanyifeng.com/#docs/class-extends)

1. 原型链

   所有实例会共享包含引用类型的原型属性

   创建子类型时无法向超类型的构造函数中传参

   ```javascript
   function SuperType() {
       this.property = true
   }
   
   SuperType.prototype.getSuperValue = function () {
       return this.property
   }
   
   function SubType() {
       this.subproperty = false
   }
   
   SubType.prototype = new SuperType()
   
   SubType.prototype.getSubValue = function () {
       return this.subproperty
   }
   
   var instance = new SubType()
   console.log(instance.getSuperValue())
   ```

2. 借用构造函数

   难以函数复用，超类型中方法对子类型不可见

   ```javascript
   function SuperType(name) {
       this.name = name
   }
   
   function SubType() {
       SuperType.call(this, 'Nicholas')
       this.age = 29
   }
   var instance = new SubType()
   console.log(instance.name)
   console.log(instance.age)
   ```

3. 组合寄生

   最常用的继承模式

   会调用两次超类型构造函数

   ```javascript
   function SuperType(name) {
       this.name = name
       this.colors = ["red", "blue", "green"]
   }
   
   SubType.prototype.sayName = function () {
       console.log(this.name)
   }
   
   function SubType(name, age) {
       SubType.call(this, name) //第二次调用
       this.age = age
   }
   
   SubType.prototype = new SuperType() //第一次调用
   SubType.prototype.constructor = SubType
   SubType.prototype.sayAge = function () {
       console.log(this.age)
   ```

4. 原型式

   ```javascript
   function object(o) {
       function F() {}
       F.prototype = o
       return new F();
   }
   // ES5 
   object.create()
   ```

5. 寄生式

   ```javascript
   function createAnother(original){
       var colne = object(original)
       colne.sayHi = function(){
           console.log("Hi")
       }
       return clone
   }
   ```

6. 寄生组合式

   ```javascript
   function inheritPrototype(subType,superType){
       var prototype = Object(superType.prototype)
       prototype.constructor = subType
       subType.prototype = prototype
   }
   ```

7. ES6 class

   ```javascript
   class Point {
   }
   
   class ColorPoint extends Point {
       constructor(x, y, color) {
         super(x, y); // 调用父类的constructor(x, y)
         this.color = color;
       }
     
       toString() {
         return this.color + ' ' + super.toString(); // 调用父类的toString()
       }
     }
   ```

------

PS：在VS Code里写代码时，可以有工具直接把所有构造函数转化成类的形式，还蛮好玩的。