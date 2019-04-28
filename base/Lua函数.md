## Lua 函数
函数是对语句和表达式进行抽象的主要方法。既可以用来处理一些特殊的工作，也可以用来计算一些值。

Lua 提供了许多的内建函数，你可以很方便的在程序中调用它们，如 `print()` 函数可以将传入的参数打印在控制台上。

Lua 函数主要有两种用途：
- 完成指定的任务，这种情况下函数作为调用语句使用；
- 计算并返回值，这种情况下函数作为赋值语句的表达式使用。

### 一、函数定义
格式如下：
```lua
optional_function_scope function function_name(argument1, argument2, argument3..., argumentn)
   function_body
 return result_params_comma_separated
end
```

- `optional_function_scope`：该参数是可选的制定函数是全局函数还是局部函数，未设置该参数末尾为全局函数，如果你需要设置函数为局部函数需要使用关键字 `local`。
- `function_name`：指定函数名称。
- `argument1, argument2, argument3..., argumentn`：函数参数，多个参数以逗号隔开，函数也可以不带参数。
- `function_body`：函数体，函数中需要执行的代码语句块。
- `result_params_comma_separated`：函数返回值，Lua语言函数可以返回多个值，每个值以逗号隔开。

以下是 `max()` 函数的源代码。 此函数接受两个参数num1和num2，并返回两者的最大值
```lua
--[[ 函数返回两个值的最大值 --]]
function max(num1, num2)

   if (num1 > num2) then
      result = num1;
   else
      result = num2;
   end

   return result; 
end

-- 调用函数
print("两值比较最大值为 ",max(10, 4))
print("两值比较最大值为 ",max(5, 6))
```

### 二、传递函数
在Lua中，将函数赋值给变量，也可以将它们作为另一个函数的参数传递。 下面是一个简单的例子，实现在Lua中分配和传递函数作为参数。
```lua
myprint = function(param)
   print("这是打印函数 -   ##", param, "##")
end

function add(num1, num2, functionPrint)
   result = num1 + num2
   -- 调用传递的函数参数
   functionPrint(result)
end

myprint(10)

-- myprint 函数作为参数传递
add(2, 5, myprint)
```
以上代码执行结果为：
```
这是打印函数 -   ##   10  ##
这是打印函数 -   ##   7   ##
```

### 三、可变参数的函数
使用 `...` 作为参数在Lua中创建具有可变参数的函数。

下面是通过函数计算返回平均值，并采用变量参数的示例。
```lua
function average(...)
   result = 0
   local arg={...}
   for i,v in ipairs(arg) do
      result = result + v
   end
   print("总共传入 " .. #arg .. " 个数")
   return result/#arg
end

print("平均值为",average(10,5,3,4,5,6))
```
运行结果：
```
总共传入 6 个数
平均值为	5.5
```

### 四、多返回值
Lua 函数可以返回多个结果值，比如自带的 `string.find`，其返回匹配串"开始和结束的下标"（如果不存在匹配串返回 `nil`）。
```lua
s, e = string.find("www.ibloger.net", "ibloger") 
print(s, e)             -->  5	11
```

Lua 函数中，在 `return` 后列出要返回的值得列表即可返回多值，如：
```lua
function maximum (a)
    local mi = 1             -- 最大值索引
    local m = a[mi]          -- 最大值
    for i,val in ipairs(a) do
       if val > m then
           mi = i
           m = val
       end
    end
    return m, mi
end

print(maximum({8, 10, 23, 12, 5}))      --> 23   3
```

