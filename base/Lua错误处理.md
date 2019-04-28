## Lua 错误处理
程序运行中错误处理是必要的，在我们进行文件操作，数据转移及web service 调用过程中都会出现不可预期的错误。如果不注重错误信息的处理，就会造成信息泄露，程序无法运行等情况。

任何程序语言中，都需要错误处理。错误类型有：
- 语法错误
- 运行错误

### 一、语法错误
语法错误通常是由于对程序的组件（如运算符、表达式）使用不当引起的。一个简单的实例如下：
```lua
a == 2
```

以上代码执行结果为：
```lua
lua: test.lua:2: syntax error near '=='
```
正如你所看到的，以上出现了语法错误，一个 "=" 号跟两个 "=" 号是有区别的。一个 "=" 是赋值表达式两个 "=" 是比较运算。

另外一个实例:
```lua
for a= 1,10
   print(a)
end
```
执行以上程序会出现如下错误：
```lua
lua: test2.lua:2: 'do' expected near 'print'
```
语法错误比程序运行错误更简单，运行错误无法定位具体错误，而语法错误我们可以很快的解决，如以上实例我们只要在 `for` 语句下添加 `do` 即可：
```lua
for a= 1,10
do
   print(a)
end
```

### 二、运行错误
运行错误是程序可以正常执行，但是会输出报错信息。如下实例由于参数输入错误，程序执行时报错：
```lua
function add(a,b)
   return a+b
end

add(10)
```
当我们编译运行以下代码时，编译是可以成功的，但在运行的时候会产生如下错误：
```lua
lua: test2.lua:2: attempt to perform arithmetic on local 'b' (a nil value)
stack traceback:
 test2.lua:2: in function 'add'
  test2.lua:5: in main chunk
    [C]: ?
```
以下报错信息是由于程序缺少 `b` 参数引起的。

### 三、错误处理 - 断言和错误功能
我们可以使用两个函数：`assert` 和 `error` 来处理错误。

实例如下：
#### 1、assert 函数
```lua
local function add(a,b)
   assert(type(a) == "number", "a 不是一个数字")
   assert(type(b) == "number", "b 不是一个数字")
   return a+b
end

add(10)
```
执行以上程序会出现如下错误：
```lua
lua: test.lua:3: b 不是一个数字
stack traceback:
	[C]: in function 'assert'
	test.lua:3: in local 'add'
	test.lua:6: in main chunk
	[C]: in ?
```
实例中 `assert` 首先检查第一个参数，若没问题，assert 不做任何事情；否则，assert 以第二个参数作为错误信息抛出。

#### 2、error 函数
语法格式：
```lua
error (message [, level])
```
功能：终止正在执行的函数，并返回 `message` 的内容作为错误信息(`error` 函数永远都不会返回)

通常情况下，`error` 会附加一些错误位置的信息到 `message` 头部。

Level参数指示获得错误的位置:

- `Level=1`[默认]：为调用 error 位置(文件+行号)
- `Level=2`：指出哪个调用 error 的函数的函数
- `Level=0`：不添加错误位置信息

### 四、pcall 和 xpcall
在 Lua 编程中，为了避免抛出这些错误和处理错误，需要使用 `pcall` 或 `xpcall` 函数。

`pcall(f，arg1，...)` 函数在保护模式下调用所请求的函数。如果函数 `f` 中发生某些错误，则不会抛出错误。它只返回错误状态。

使用 `pcall` 的一个简单示例如下所示。
```lua
function myfunction()
   n = n/nil
end

if pcall(myfunction) then
   print("Success")
else
   print("Failure")
end
```
当构建上面程序时，它将成功构建并运行。运行后，显示运行时错误。
```
Failure
```

`xpcall(f，err)` 函数调用所请求的函数，并设置错误处理程序。`f` 内的任何错误都不会传播; 而 `xpcall` 只捕获错误，使用原始错误对象调用 `err` 函数，并返回状态代码。

使用 `xpcall` 函数的一个简单示例如下所示。
```lua
function myfunction()
   n = n/nil
end

function myerrorhandler(err)
   print("ERROR:", err)
end

status = xpcall(myfunction, myerrorhandler)

print(status)
```
当运行上面的程序时，将得到以下输出。
```
ERROR:	input:2: attempt to perform arithmetic on a nil value (global 'n')
false
```

作为程序员，最重要的是确保在编写的程序中处理正确的错误处理。 使用错误处理可以确保处理超出边界条件的意外条件而不会干扰程序的用户。
