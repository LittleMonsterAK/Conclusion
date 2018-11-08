javascript是一门解释执行的语言，在js中并没有`class`这种定义，es6中的`class`也只是一个语法糖。但是它有类似于`class`的结构`function`。所以可以说js是一门基于`function`的语言。虽然js的这种"类"实现的不一样，但是它也做到继承。下面就说下js是如何实现继承的。

下面看一个例子：
```
function Animal() {
}

Animal.prototype.say = function () {
  console.log('this is animal');
}

var animal1 = new Animal();
animal.say();
console.log(animal1 instanceof Object);
```

输出
```
this is animal
true
```

上面是一个使用原型链定义的一个对象原型, 调用new操作符后返回一个实例对象，对这个对象判断得出是继承自`Object`, 那js是如何实现继承的呢？

定义：

> 对于每个实例对象来说，他们都有一个隐藏属性[[Prototype]]这个属性指向创建它的原型，例如上面的animal1的`[[Prototype]]`指向Animal的prototype。还有我们需要介绍下instanceof的原理，根据文档

> instanceof 运算符用来检测 constructor.prototype 是否存在于参数 object 的原型链上。

也就是说 animal1 的原型链上有 Object.prototype, 对象的 `[[Prototype]]` 可以从通过Object.getPrototypeOf 来获取到

做个实验：

```
console.log(Object.prototype === Object.getPrototypeOf(animal1));
console.log(Object.prototype === Object.getPrototypeOf(Object.getPrototypeOf(Animal.prototype)));
```

输出：

```
false
true
```

以上结果可以得出 Object.prototype 存在于对象 animal1 的原型链中。所以animal1 是继承自 Object.

接下去我们做另一个例子来实现我们的继承关系

```
function Animal() {
}

Animal.prototype.say = function () {
  console.log('this is animal');
}

function Cat () {

}

Cat.prototype = new Animal();

Cat.prototype.eating = function () {
  console.log('i\'m eating');
}

Cat.prototype.constructor = Cat;

var my\_cat = new Cat();

console.log(my\_cat instanceof Animal);
```

输出
```
true

```

可以看出cat已经继承了animal。
上面的代码中
```
Cat.prototype = new Animal()
```
这行使得 `Cat` 的的原型链中存在了 `Animal.prototype` 。 因为new Animal() 的赋值使得 `Cat.prototype.[[Prototype]] = Animal.prototype` 。
当然上面的这种赋值会覆盖原先 `Cat.prototype` 中的一些属性，比如`constructor`。这个属性简单介绍下，它原本应该指向对应的构造函数，在es5中它作用仅仅如此，但是在es6中会使用到整个属性，所以为了保证代码的兼容性，必须给他赋上原值。

总结：

> 在javascript中使用[[Prototype]]这个隐形属性来作为继承链，当我们需要让A原型继承B原型时，我们需要将a.Prototype.[[Prototype]] 指向 B.prototype , 同时需要恢复它原来的一些自带属性。比如constructor

下面的图是一张原型链的图, 图中`__proto__`对应 `[[prototype]]`

![avatar](prototype.jpg)
