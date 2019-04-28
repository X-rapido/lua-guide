## Lua 迭代器
迭代器（iterator）是一种对象，它能够用来遍历标准模板库容器中的部分或全部元素，每个迭代器对象代表容器中的确定的地址.

在Lua中迭代器是一种支持指针类型的结构，它可以遍历集合的每一个元素。

### 一、语法
泛型 for 在自己内部保存迭代函数，实际上它保存三个值：迭代函数、状态常量、控制变量。

泛型 for 迭代器提供了集合的 key/value 对，语法格式如下：
```lua
for k, v in pairs(t) do
    print(k, v)
end
```
- `k`：键值对的key
- `v`：键值对的Value
- `pairs(t)`：为表达式列表。

查看以下实例:
```lua
array = {"Lua", "Tutorial"}

for key,value in ipairs(array) do
   print(key, value)
end
```
以上代码执行输出结果为：
```
1  Lua
2  Tutorial
```

以上实例中使用了 Lua 默认提供的迭代函数 `ipairs`。

### 二、执行过程
泛型 for 的执行过程

- 首先，初始化，计算 `in` 后面表达式的值，表达式应该返回范性for需要的三个值：迭代函数、状态常量、控制变量；与多值赋值一样，如果表达式返回的结果个数不足三个会自动用nil补足，多出部分会被忽略。
- 第二，将状态常量和控制变量作为参数调用迭代函数（注意：对于for结构来说，状态常量没有用处，仅仅在初始化时获取他的值并传递给迭代函数）。
- 第三，将迭代函数返回的值赋给变量列表。
- 第四，如果返回的第一个值为nil循环结束，否则执行循环体。
- 第五，回到第二步再次调用迭代函数。

在 Lua 中我们常常使用函数来描述迭代器，每次调用该函数就返回集合的下一个元素。

Lua 的迭代器包含以下两种类型：无状态的迭代器、有状态的迭代器。

#### 无状态的迭代器
无状态的迭代器是指不保留任何状态的迭代器，因此在循环中我们可以利用无状态迭代器避免创建闭包花费额外的代价。

每一次迭代，迭代函数都是用两个变量（状态常量和控制变量）的值作为参数被调用，一个无状态的迭代器只利用这两个值可以获取下一个元素。

这种无状态迭代器的典型的简单的例子是 `ipairs`，他遍历数组的每一个元素。

以下实例使用了一个简单的函数来实现迭代器，实现数字 `n` 的平方：
```lua
function square(iteratorMaxCount, currentNumber)s
   if(currentNumber < iteratorMaxCount) then
      currentNumber = currentNumber+1
      return currentNumber, currentNumber*currentNumber
   end
end

for i,n in square,3,0 do
   print(i, n)
end
```
以上实例输出结果为：
```
1	1
2	4
3	9
```

稍微修改上面的代码，以模仿迭代器的ipairs函数的工作方式。输出结果相同。
```lua
function square(iteratorMaxCount, currentNumber)
   if(currentNumber < iteratorMaxCount)then
      currentNumber = currentNumber+1
      return currentNumber, currentNumber*currentNumber
   end
end

function squares(iteratorMaxCount)
   return square,iteratorMaxCount,0
end  

for i,n in squares(3) do 
   print(i,n)
end
```

#### 多状态的迭代器
上面使用函数的迭代示例不保留状态。每次调用该函数时，它都会根据发送给函数的第二个变量返回集合的下一个元素。

要保持当前元素的状态，可使用闭包(Closure)。闭包在函数调用中保留变量值。要创建一个新的闭包，这里首先创建两个函数，包括闭包本身和一个工厂，即创建闭包的函数。

如下是一个创建迭代器的例子，并将使用闭包。
```lua
array = {"Java", "JavaScript"}

function elementIterator(collection)
   local index = 0
   local count = #collection
   -- 闭包函数
   return function ()
      index = index + 1
      if index <= count then
         --  返回迭代器的当前元素
         return collection[index]
      end
   end
end

for element in elementIterator(array) do
   print(element)
end
```

输出结果：
```
Java
JavaScript
```
以上实例中我们可以看到，`elementIterator` 内使用了闭包函数，实现计算集合大小并输出各个元素。

