## Lua Debug 调试
Lua 提供了一个调试库，它提供了创建自己的调试器的原始函数。 即使没有内置的 Lua 调试器，也有许多第三方 Lua 调试器，由各种开发人员创建，其中许多是开源的。

### 一、Debug 函数

#### （1）debug()
进入用于调试的交互模式，该模式保持活动状态，直到用户只输入一行中的 `cont` 并按Enter键。 用户可以使用其他功能在此模式下检查变量。

#### （2）getfenv(object)
返回对象的环境变量。

#### （3）gethook(optional thread)
返回线程的当前挂钩设置，有三个值：当前挂钩函数，当前挂钩掩码和当前挂钩计数。

#### （4）getinfo(optional thread, function or stack level, optional flag)
返回一个包含函数信息的表。可以直接给出函数，或者可以给一个数字作为函数的值，在给定线程的调用堆栈的级别函数上运行的函数
- 级别 `0` 为当前函数(getinfo本身);
- 级别 `1` 为调用 getinfo 的函数;
- 如果 function 是一个大于活动函数数的数字，则 getinfo 返回 `nil`。

#### （5）debug.getlocal([thread,] f, local)
此函数返回在栈的 `f` 层处函数的索引为 `local` 的局部变量 的名字和值。这个函数不仅用于访问显式定义的局部变量，也包括形参、临时变量等。

#### （6）getmetatable(value)
返回给定对象的元表，如果没有元表，则返回 `nil`。

#### （7）getregistry()
返回注册表表，这是一个预定义出来的表，可以用来保存任何 C 代码想保存的 Lua 值。

#### （8）getupvalue(function, upvalue index)
此函数返回 `upvalue` 的名称和值，索引为函数 `func`。如果给定索引没有 `upvalue`，则函数返回 `nil`。

#### （9）setupvalue(f, up, value)
这个函数将 `value` 设为函数 `f` 的第 `up` 个上值。 如果函数没有那个上值，返回 `nil` 否则，返回该上值的名字。

#### （9）setfenv(function or thread or userdata, environment table)
将给定对象的环境设置为给定表，返回对象。

#### （10）sethook(optional thread, hook function, hook mask string with "c" and/or "r" and/or "l", optional instruction count)
将给定函数设置为钩子。字符串 `mask` 以及数字 `count` 决定了钩子将在何时调用。

掩码是由下列字符组合成的字符串，每个字符有其含义：
- `'c'`：每当 Lua 调用一个函数时，调用钩子；
- `'r'`：每当 Lua 从一个函数内返回时，调用钩子；
- `'l'`：每当 Lua 进入新的一行时，调用钩子。

#### （11）ssetlocal(optional thread, stack level, local index, value)
将 `value` 赋这个函数的第 `level` 级别函数的第 `local` 个局部变量。如果没有那个变量，函数返回 `nil` 。 如果 `level` 越界，抛出一个错误。

#### setmetatable(value, table)
将 `value` 的元表设为 `table` （可以是 `nil`）。 返回 `value`。

#### tracebacks([thread,] [message [, level]])
如果 `message` 有，且不是字符串或 `nil`，函数不做任何处理直接返回 `message`。否则，返回调用栈的栈回溯信息。

字符串可选项 `message` 被添加在栈回溯信息的开头。数字可选项 `level` 指明从栈的哪一层开始回溯 （默认为 1，即调用 `traceback` 的那里）。

### 二、示例
```lua
function myfunction ()
    print(debug.traceback("Stack trace"))
    print(debug.getinfo(1))
    print("Stack trace end")

    return 10
end

myfunction ()
print(debug.getinfo(1))
```
执行结果
```lua
➜  code-test lua test.lua
Stack trace
stack traceback:
	test.lua:2: in function 'myfunction'
	test.lua:7: in main chunk
	[C]: in ?
table: 0x7ff4cc500580
Stack trace end
table: 0x7ff4cc407130
```
在上面的示例程序中，使用调试库中提供的 `debug.trace` 函数打印堆栈跟踪。`debug.getinfo` 获取函数的当前表。

### 三、示例
调试示例开发人员经常为了知道函数的局部变量以进行调试。 为此，可以使用 `getupvalue` 和使用 `setupvalue` 并设置这些局部变量。

一个简单的例子如下所示。
```lua
function newCounter ()
   local n = 0
   local k = 0

   return function ()
      k = n
      n = n + 1
      return n
   end

end

counter = newCounter ()

print(counter())		--> 1
print(counter())		--> 2

local i = 1

repeat
   name, val = debug.getupvalue(counter, i)

   if name then
      print("index", i, name, "=", val)

      if(name == "n") then
         debug.setupvalue(counter, 2, 10)
      end

      i = i + 1
   end
until not name

print(counter())
```

执行结果
```
1
2
index	1	k	=	1
index	2	n	=	2
11
```
在此示例中，计数器每次调用时都会更新一次。可以使用 `getupvalue` 函数查看局部变量的当前状态。然后将局部变量设置为新值。这里，在调用 `set` 操作之前 `n` 为 `2`。使用 `setupvalue` 函数，它更新为 `10`。现在当调用计数器函数时，它将返回 `11` 而不是 `3`。

### 四、调试类型
- 命令行调试
- 图形界面调试

#### 命令行调试
命令行调试是使用命令和打印语句在命令行进行调试的调试类型。

Lua 有许多命令行调试器，下面列出了一些。
- `RemDebug`：它是 Lua 5.0 和 Lua 5.1 的远程调试器。它允许远程控制另一个Lua程序的执行，设置断点并检查程序的当前状态。它还可以调试 CGILua 脚本。
- `clidebugger`：它是 Lua 5.1 的简单命令行界面调试器，用纯Lua编写。除了标准的 Lua 5.1 库之外，它不依赖于任何其他东西。它的灵感来自 RemDebug，但没有远程工具。
- `ctrace`：用于跟踪Lua API调用的工具。
- `xdbLua`：用于Windows平台的简单Lua命令行调试程序。
- `LuaInterface Debugger`：它是 LuaInterface 的调试器扩展。它将内置的Lua调试接口提升到更高的级别。与调试器的交互由事件和方法调用完成。
- `Rldb`：它是一个通过套接字的远程Lua调试器，可在 Windows 和 Linux 上使用。它可以为您提供比现有功能更多的功能。
- `ModDebug`：这允许远程控制另一个Lua程序的执行，设置断点，并检查程序的当前状态。

#### 图形调试
在IDE下可以进行图形调试，在IDE中可为各种状态（如变量值，堆栈跟踪和其他相关信息）进行可视化调试。借助断点，步入，步进和IDE中的其他按钮，可以实现可视化表示和逐步控制执行。

Lua有许多图形调试器，下面列出了一些。
- `SciTE`：Lua 的默认 Windows IDE 提供了多个调试工具，如断点，步骤，步入，步进，监视变量等。
- `Decoda`：这是一个具有远程调试支持的图形调试器。
- `ZeroBrane Studio `：Lua IDE 集成了远程调试器，堆栈视图，监视视图，远程控制台，静态分析器等。适用于 LuaJIT，Love2d，Moai 和其他Lua引擎; Windows，OSX和Linux。
- `akdebugger`：这是Eclipse调试器和编辑器的Lua插件。
- `luaedit`：这包括远程调试，本地调试，语法突出显示，完成建议列表，参数命题引擎，高级断点管理（包括断点和命中计数的条件系统），功能列表，全局和局部变量列表，监视，面向解决方案的管理。


