---
title: javascript函数
date:
tags: '笔记'
categories: js语言精粹
---

## 函数  

------
函数用于指定对象的行为，在其中包含一组语句。用于代码复用、信息隐藏、和组合调用。  

### 函数对象  
JavaScript 中的函数就是对象。函数对象连接到`Function.prototype`（该原型对象本身连接到`Object.prototype`）。每个函数对象在创建时会附加两个隐藏属性：`函数的上下文`和`实现函数行为的代码`。

函数对象在创建时也随配有一个`prototype`属性。它的值是一个拥有constructor属性且值即为该函数对象。  
```JS
    function add(a,b){
        return a+b;
    }
```  
因为函数是对象，所以可以像任何其他的值一样被使用。函数可以保存在变量、对象和数组中。函数可以被当做参数传递给其他函数，函数也可以再返回函数。函数也可以拥有方法。

函数的与众不同之处在于可以被调用。  
### 函数字面量  
函数对象通过函数字面量来创建。
函数字面量包含4部分，分别是：保留字 function、函数名、参数、花括号中的语句。
```js
    var add = function(a, b) {
        return a + b
    }  
```  
### 调用  
调用一个函数会暂停当前函数的执行，传递控制权和参数给新函数。除了声明时定义的形式参数，还有两个附加参数：`this`和`arguments`。参数this在面向对象编程中非常重要，它的值取决于调用的模式。

实参和形参个数不匹配时，不会有运行时错误。实参过多时，超出的实参被忽略。形参过多时，缺失的值被替换为undefined。  
- 方法调用模式  
    当一个函数被保存为对象的一个属性时，我们称它为一个方法。当一个方法被调用时，`this被绑定到该对象`,通过this可以对该对象的属性和方法进行调用和操作。  
    ```js
    var myObject = {
        text: hello,
        say:function(msg){
            console.log(this.text + msg);
        }
    }
    myObject.say(world);

    ```
- 函数调用模式  
当一个函数并非一个对象的属性时，那么它就是被当做一个函数来调用的。
此时this被绑定到全局对象。即时是内部函数也会将this绑定到全局对象。this的指向问题 作用域不一样 this的指向就不一样,可以在函数内创建一个属性并赋值为this来解决这个问题,一般通过`var that = this`;  
```js  
    var add = function(a, b) {
        return a + b
    }

    myObject.double = function() {
        var that = this
        var helper = function() {
            that.value = add(that.value, that.value)
        }
        helper()
    }

    myObject.double()
    console.log(myObject.value) //8
```
```js
        attachEvent: function () {
        // attachEvent函数的作用域
        // js 里面this总会指向什么
        // 对象的方法被执行时 this指向对象
        // console.log(this);
        var that = this;
        this.staticElement.addEventListener('click', function () {
            // 这个函数被执行时，并不是对象的方法，而是匿名函数，作为事件处理函数来执行，this会指向事件发生元素
            // this的指向跟函数的调用方式有关
            // this的指向问题 作用域不一样 this的指向就不一样
            // 作用域 事件的回调函数
            // console.log(this);
            // alert(that.value);
            that.convertToEdit();
        }, false);
        this.filedElement.addEventListener('keydown', function (evt) {
            // 在事件发生的一刹那 会产生一个临时事件对象
            console.log(evt);
            if (evt.keyCode == 13) {
                that.staticElement.innerHTML = this.value;
                console.log(that.staticElement.innerHTML);
                that.convertToText();
            }
        }, false)
    }
```
- 构造器调用模式  
JavaScript是一门基于原型继承的语言。对象可以直接从其他对象继承属性。该语言是无类型的。
如果在一个函数前面带上new来调用，那么背地里将会创建一个连接到该函数的prototype成员的新对象，同时this会被绑定到那个新对象上。  
```js
    //创建构造器函数
    var Quo = function(string) {
        this.status = string
    }

    //给Que的所有实例提供一个公共方法
    Quo.prototype.getStatus = function() {
        return this.status
    }

    //实例化
    var myQuo = new Quo('confused')

    console.log(myQuo.getStatus()) //confused
```
- Apply 调用模式  
`apply`方法让我们构建一个参数数组传递给调用函数。他也允许我们选择`this`的值。apply方法接受两个参数，第一个是要绑定给`this`的值，第二个是参数数组。
```js
    var arr = [3, 4]
    var sum = add.apply(null, arr)
    console.log(sum) //7

    var statusObject = {
        status: 'hello'
    }

    var status = Quo.prototype.getStatus.apply(statusObject)
    console.log(status) //hello
```  
### 参数  
当函数被调用时，会得到一个`arguments`数组。通过此参数可以访问所有它被调用时传递给它的`参数列表`，包括那些没有被分配给函数声明时定义的形参的多余参数。这使得编写一个无须指定参数个数的函数成为可能。
```js

    var sum = function() {
        var i, sum = 0
        for (i = 0; i < arguments.length; i++) {
            sum += arguments[i]
        }
        return sum
    }
    console.log(sum(1, 2, 3, 4, 5, 6, 7, 8, 9)) //45
```
因语言的设计错误，`arguments`并不是一个真正的数组。是一个“类似数组”的对象。有`length`属性，但没有任何数组的方法。

### 返回  
函数执行时遇到关闭函数体的`}`时结束。然后把控制权交还给调用该函数的程序。

`return`可以使函数提前返回，当return语句执行时函数立即返回不在执行余下的语句。

函数总是会返回一个值，若没有指定，则返回`undefined`。

若函数调用时在前面加上了`new`前缀，且返回值不是一个对象的时候，则返回`this`（该新对象）。  
### 闭包

作用域的好处是内部函数可以访问定义他们的外部函数的参数和变量（除了`this`和`arguments`）。
```js
    var quo = function(status) {
        return {
            get_status: function() {
                return status;
            }
        }
    }

    var myQuo = quo('amazed')
    console.log(myQuo.get_status()) //amazed
```
狭义的说，返回的那个对象即闭包，它里面的方法可以访问它被创建时所处的上下文环境。`status`访问的就说`对象中的status`属性本身,通过闭包可以让一个局部变量驻留在内存中

避免在循环中创建函数，容易引起混淆。可以现在循环之外创建一个辅助函数，让辅助函数在返回一个绑定了当前i值的函数，这样就不会导致混淆了。  

### 回调

将一个函数作为参数，一旦接收到响应，再调用这个函数。
```js
     fs.readFile('demo/02.js','utf8',(error,data)=>{
     if(error) throw error;
     //在异步中的错误不能被捕获 通过throw error进行错误的捕获
     console.log(data) >>a.txt;
    });
```
### 模块
可以用函数和闭包构造模块。模块是一个提供接口却隐藏与实现的函数或者对象

模块模式的一般形式是：一个定义了私有变量和函数的函数；利用闭包创建可以访问私有变量和函数的特权函数；最后返回这个特权函数，或者把他们保存到一个可访问到的地方。
```js
    var numberCal = (function() {
        var half = function(n) {
            return n / 2
        }
        var double = function(n) {
            return n * 2
        }
        var tribble = function(n) {
            return n * 3
        }
        return {
            half: half,
            double: double,
            tribble: tribble
        }
    }())

    console.log(numberCal.half(5)) //2.5
    console.log(numberCal.half(6)) //3
    console.log(numberCal.double(7)) //14
    console.log(numberCal.tribble(7)) //21
```
### 级联

如果让方法返回this而不是默认的undefined，就可以启用级联，即连续调用。


### 记忆

函数可以将先前操作的结果记录在某个对象或者数组里，从而避免无谓的重复运算。这种优化被称为记忆（memoization）。






