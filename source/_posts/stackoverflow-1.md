title: (每周爆栈精选)javascript关于作用域的几个例子
date: 2013-10-19 17:19:44
tags: [old blog, scope, stackoverflow, javascript]
---

这是我2年前写的blog，原文在[这里](http://blog.163.com/harrychen66@126/blog/static/16546854201391951944863/)

来自爆栈[这里](http://stackoverflow.com/questions/500431/javascript-variable-scope)

```javascript
// 全局作用域变量定义
var a=1;

// 简单的例子：访问全局作用域
function one(){
    alert(a);
}

// 简单的例子：访问的是局部作用域
function two(a){
    alert(a);
}

// 简单的例子：还是局部作用域
function three(){
  var a = 3;
  alert(a);
}

// 有点难度的例子：在函数内脚本块里面定义的局部变量，可以被函数内部访问的局部作用域的变量
function four(){
    if(true){
        var a=4;
    }

    alert(a); // alerts '4', not the global value of '1'
}


// 有点难度的例子：对象属性作用域
function Five(){
    this.a = 5;
}


// 高级货: 闭包
var six = function(){
    var foo = 6;

    return function(){
        // javascript "闭包" 意思是我只能在这个函数里面访问到foo,
        alert(foo);
    }
}()


// 另外一个高级货: 基于prototype的作用域
function Seven(){
  this.a = 7;
}

// [object].prototype.property 方式定义属性会在 [object].property 方式定义后丢失对应的属性
Seven.prototype.a = -1; // 此值会被覆盖，因为构造函数会将a属性重新复制
Seven.prototype.b = 8; // 同理此b属性没有被覆盖，所以值为8



// 以上例子的值分别为 1-8
one();
two(2);
three();
four();
alert(new Five().a);
six();
alert(new Seven().a);
alert(new Seven().b);
```

```javascript
我补充几个高级货：

// this作用域的区别
var nine = 9;
function Nine(){
	alert(this.nine); // this指向window的全局作用域
}
Nine();
// 以上的直接调用Nine()的效果，但是this对象作用域可以变的
Nine.call({nine : 10});// this对象指向参数的对象

// 除了call和apply，还有另外一种方式调用会导致不同的this引用对象
var Eleven = 12;
var bar = {
	Eleven : 11,
	foo : function(){
		alert(this.Eleven)
	}
};
bar.foo();// this指向bar对象
var foo = bar.foo;
foo();// this指向window
// 以上例子分别输出9-12

```