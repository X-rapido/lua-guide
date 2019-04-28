## Lua 面向对象编程

面向对象编程（Object Oriented Programming，OOP）是一种非常流行的计算机编程架构。

以下几种编程语言都支持面向对象编程：
- C++
- Java
- Objective-C
- Smalltalk
- C#
- Ruby

### 一、面向对象的特点
- 类：类是用于创建对象的可扩展模板，为状态(成员变量)和行为实现提供初始值。
- 对象：它是类的一个实例，并为自己分配了单独的内存。
- 继承：这是一个概念，通过该概念，一个类的变量和函数由另一个类继承。
- 封装：这是在类中组合数据和函数的过程。借助函数可以在类的外部访问数据。 它也被称为数据抽象。

### 二、Lua 面向对象
我们知道，对象由属性和方法组成。Lua 中最基本的结构是 `table`，所以需要用 `table` 来描述对象的属性。

Lua 中 `function` 可以用来表示方法。那么类就可以通过 `table + function` 模拟出来。

至于继承，可以通过 `metetable` 模拟出来（不推荐用，只模拟最基本的对象大部分时间够用了）。

Lua 中的表不仅在某种意义上是一种对象。像对象一样，表也有状态（成员变量），也有与对象的值独立的本性，特别是拥有两个不同值的对象（table）代表两个不同的对象；一个对象在不同的时候也可以有不同的值，但他始终是一个对象；

### 三、面向对象示例
考虑一个简单的数学例子。我们经常遇到处理不同形状的情况，如圆形，矩形和方形。

形状可以具有共同的属性 `area`。因此，使用公共属性区域从基础对象形状扩展其他形状。每个形状都可以有自己的属性，像矩形这样的函数可以具有属性 `length`，`width`，`area` 作为属性，`printArea` 和 `calculateArea` 作为它的函数。

#### 1、创建一个简单的类
下面显示了具有三个属性 `length`，`width` 和 `area` 的矩形的简单类实现。它还有一个 `printArea` 函数来打印计算区域面积。

```lua
-- Meta class
Rectangle = {area = 0, length = 0, breadth = 0}

-- 派生类的方法 new
function Rectangle:new(o, length, breadth)
  o = o or {}
  setmetatable(o, self)
  self.__index = self
  self.length = length or 0
  self.breadth = breadth or 0
  self.area = length * breadth;
  return o
end

-- 派生类的方法 printArea
function Rectangle:printArea ()
  print("矩形面积为 ", self.area)
end
```

#### 2、创建一个对象
创建对象是为类实例分配内存的过程。每个对象都有自己的内存并共享公共类数据。
```lua
r = Rectangle:new(nil,10,20)
```

#### 3、访问属性
可以使用点(`.`)运算符访问类中的属性。
```lua
print(r.length)
```

#### 4、访问成员函数
使用带有对象的冒号(`:`)运算符访问成员函数。
```lua
r = Rectangle:new(nil,10,20)
r:printArea()
```

分配内存并设置初始值。
#### 5、完整实例
以下我们演示了 Lua 面向对象的完整实例：
```lua
-- Meta class
Rectangle = {area = 0, length = 0, breadth = 0}

-- 派生类的方法 new
function Rectangle:new(o, length, breadth)
  o = o or {}
  setmetatable(o, self)
  self.__index = self
  self.length = length or 0
  self.breadth = breadth or 0
  self.area = length * breadth;
  return o
end

-- 派生类的方法 printArea
function Rectangle:printArea ()
  print("矩形面积为 ", self.area)
end

-- 创建对象
myshape = Rectangle:new(nil, 10, 20)

-- 执行成员函数
myshape:printArea()
```

执行以上程序，输出结果为：
```
面积为     200
```

### 四、Lua 继承
继承是指一个对象直接使用另一对象的属性和方法。可用于扩展基础类的属性和方法。

以下演示了一个简单的继承实例：
```lua
-- Meta class
Shape = {area = 0}

-- 基础类方法 new
function Shape:new(o, side)
  o = o or {}
  setmetatable(o, self)
  self.__index = self
  side = side or 0
  self.area = side * side;
  return o
end

-- 基础类方法 printArea
function Shape:printArea ()
  print("面积为 ",self.area)
end
```

接下来的实例，`Square` 对象继承了 `Shape` 类:
```lua
Square = Shape:new()
-- 派生新类
function Square:new (o, side)
  o = o or Shape:new(o,side)
  setmetatable(o, self)
  self.__index = self
  return o
end
```

#### 完整实例
以下实例我们继承了一个简单的类，来扩展派生类的方法，派生类中保留了继承类的成员变量和方法。
```lua
-- Meta class
Shape = {area = 0}
-- 基础类方法 new
function Shape:new (o,side)
  o = o or {}
  setmetatable(o, self)
  self.__index = self
  side = side or 0
  self.area = side*side;
  return o
end
-- 基础类方法 printArea
function Shape:printArea ()
  print("面积为 ",self.area)
end

-- 创建对象
myshape = Shape:new(nil,10)
myshape:printArea()


Square = Shape:new()
-- 派生类方法 new
function Square:new (o,side)
  o = o or Shape:new(o,side)
  setmetatable(o, self)
  self.__index = self
  return o
end

-- 派生类方法 printArea
function Square:printArea ()
  print("正方形面积为 ",self.area)
end

-- 创建对象
mysquare = Square:new(nil,10)
mysquare:printArea()


Rectangle = Shape:new()
-- 派生类方法 new
function Rectangle:new (o,length,breadth)
  o = o or Shape:new(o)
  setmetatable(o, self)
  self.__index = self
  self.area = length * breadth
  return o
end

-- 派生类方法 printArea
function Rectangle:printArea ()
  print("矩形面积为 ",self.area)
end

-- 创建对象
myrectangle = Rectangle:new(nil,10,20)
myrectangle:printArea()
```
执行以上代码，输出结果为：
```
面积为    100
正方形面积为     100
矩形面积为  200
```
在上面的例子中，创建了两个派生类：`Square` 和 `Rectangle`。并在派生类中覆盖基类的函数。在此示例中，派生类会覆盖函数 `printArea`。

#### 函数重写
Lua 中我们可以重写基础类的函数，在派生类中定义自己的实现方式：
```lua
-- 派生类方法 printArea
function Square:printArea ()
  print("正方形面积 ", self.area)
end
```
