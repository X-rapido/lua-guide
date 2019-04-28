## Lua 元表（Metatable）
在 Lua `table` 中我们可以访问对应的 `key` 来得到 `value` 值，但是却无法对两个 table 进行操作。

因此 Lua 提供了元表（`Metatable`），允许我们改变 table 的行为，每个行为关联了对应的元方法（`MetaMethod`）。

例如：使用元表可以定义计算两个 table 的相加操作。先检查两者之一是否有元表，之后检查是否有一个叫 `__add` 的元方法，若找到，则调用对应的值。

### 一、设置与获取
有两个很重要的函数来处理元表：
- `setmetatable(table, metatable)`：为表设置元表。
- `getmetatable(table)`：获取表的元表。

如下示例：对指定的表设置元表。
```lua
mytable = {}                          -- 普通表 
mymetatable = {}                      -- 元表
setmetatable(mytable, mymetatable)    -- 把 mymetatable 设为 mytable 的元表 
```

等同于
```lua
mytable = setmetatable({},{})
```

如下示例：返回对象元表。
```lua
getmetatable(mytable)                 -- 返回 mymetatable
```

### 二、__index 元方法
这是 `metatable` 最常用的键。

当你通过键来访问 table 的时候，如果这个键有值就直接返回；如果没有值，那么 Lua 就会寻找该 table 的 `metatable`，假定 `metatable` 中有 `__index` 键。如果 `__index` 包含一个表格，Lua 会在表格中查找相应的键。

我们可以在使用 lua 命令进入交互模式查看：

```lua
Lua 5.3.5  Copyright (C) 1994-2018 Lua.org, PUC-Rio
> other = {foo=3}
> t = setmetatable({}, {__index = other})
> t.foo
3
> t.abc
nil
>
```

如果 `__index` 包含一个函数的话，Lua 就会调用那个函数，table 和键会作为参数传递给函数。

`__index` 元方法查看表中元素是否存在，如果不存在，返回结果为 `nil`；如果存在则由 `__index` 返回结果。

```lua
mytable = setmetatable({key1 = "value1"}, {
  __index = function(mytable, key)
    if key == "key2" then
      return "metatablevalue"
    else
      return nil
    end
  end
})

print(mytable.key1, mytable.key2)       --> value1	metatablevalue
```
解析：

- `mytable` 表赋值为 `{key1 = "value1"}`。
- `mytable` 设置了元表，元方法为 `__index`。
- 在 `mytable` 表中查找 `key1`，如果找到，返回该元素，找不到则继续。
- 在 `mytable` 表中查找 `key2`，如果找到，返回该元素，找不到则继续。
- 判断元表有没有 `__index` 方法，如果 `__index` 方法是一个函数，则调用该函数。
- 元方法中查看是否传入 `key2` 键的参数（示例中 `mytable.key2` 已设置），如果传入 `key2` 参数返回 `metatablevalue`，否则返回 `mytable` 对应的键值。

以上代码可以简化为：
```lua
mytable = setmetatable({key1 = "value1"}, { __index = {key2 = "metatablevalue"} })

print(mytable.key1, mytable.key2)
```

### 三、__newindex 元方法
`__newindex` 元方法用来对表更新，`__index` 则用来对表访问 。

当将 `__newindex` 添加到 `metatable` 时，如果表中没有键，则新键的行为将由元方法定义。

当你给表的一个缺少的索引赋值，解释器就会查找 `__newindex` 元方法：如果存在则调用这个函数而不进行赋值操作。

如下示例：当主表中没有索引时设置 `metatable` 索引。
```lua
mymetatable = {}
mytable = setmetatable({key1 = "value1"}, { __newindex = mymetatable })

print(mytable.key1)                         --> value1

mytable.newkey = "新值2"
print(mytable.newkey, mymetatable.newkey)   --> nil	新值2

mytable.key1 = "新值1"
print(mytable.key1, mymetatable.newkey1)    --> 新值1	nil
```

以上实例中表设置了元方法 `__newindex`，在对新索引键（newkey）赋值时（`mytable.newkey = "新值2"`），会调用元方法，而不进行赋值。而如果对已存在的索引键（key1），则会进行赋值，而不调用元方法 ` __newindex`。

#### rawset 函数

以下实例使用了 `rawset` 函数来更新表：
```lua
mytable = setmetatable({key1 = "value1"}, {
  __newindex = function(mytable, key, value)
        print(key, value)
        rawset(mytable, key, "\""..value.."\"")
  end
})

mytable.key1 = "new value"
mytable.key2 = 4
mytable.key3 = 'abc'

print(mytable.key1, mytable.key2, mytable.key3)
```
执行结果
```
key2	4
key3	abc
new value	"4"	"abc"
```
`rawset` 设置值而不使用元表的 `__newindex`。 类似地，有一个 `rawget` 可以在不使用 `__index` 的情况下获取值。

### 四、元素添加操作符
以下实例演示了两表相加操作：
```lua
-- 两表相加操作
mytable = setmetatable({ 1, 2, 3 }, {
    __add = function(mytable, newtable)
        for i = 1, #(newtable) do
            table.insert(mytable, #(mytable)+1, newtable[i])   -- 插入
        end
        return mytable
    end
})

secondtable = {4, 5, 6}
mytable = mytable + secondtable

for k,v in ipairs(mytable) do
    print(k,v)
end
```
执行结果
```
1	1
2	2
3	3
4	4
5	5
6	6
```

`__add` 是元方法（MetaMethod），这是 Lua 内建约定的，其它的还有如下的 MetaMethod

| 模式                  | 描述                                   |
| --------------------- | -------------------------------------- |
| `__add(a, b)`         | 对应表达式 `a + b`                     |
| `__sub(a, b)`         | 对应表达式 `a - b`                     |
| `__mul(a, b)`         | 对应表达式 `a * b`                     |
| `__div(a, b)`         | 对应表达式 `a / b`                     |
| `__mod(a, b)`         | 对应表达式 `a % b`                     |
| `__pow(a, b)`         | 对应表达式 `a ^ b`                     |
| `__unm(a)`            | 对应表达式 `-a`                        |
| `__concat(a, b)`      | 对应表达式 `a .. b`                    |
| `__len(a)`            | 对应表达式 `#a`                        |
| `__eq(a, b)`          | 对应表达式 `a == b`                    |
| `__lt(a, b)`          | 对应表达式 `a < b`                     |
| `__le(a, b)`          | 对应表达式 `a <= b`                    |
| `__index(a, b)`       | 对应表达式 `a.b`                       |
| `__newindex(a, b, c)` | 对应表达式 `a.b = c`                   |
| `__call(a, ...)`      | 对应表达式 `a(...)`                    |

#### __call 元方法
`__call` 元方法在 Lua 调用一个值时调用。

以下实例演示了计算表中元素的和：
```lua
mytable = setmetatable({10}, {
    __call = function(mytable, newtable)
	    sum = 0
	    for i = 1, #(mytable) do
		    sum = sum + mytable[i]
	    end
        for i = 1, #(newtable) do
		    sum = sum + newtable[i]
	    end
	    return sum
    end
})

newtable = {10,20,30}

print(mytable(newtable))        --> 70
```

#### __tostring 元方法
`__tostring` 元方法用于修改表的输出行为。以下实例我们自定义了表的输出内容：
```lua
mytable = setmetatable({ 10, 20, 30 }, {
    __tostring = function(mytable)
        sum = 0
        for k, v in pairs(mytable) do
            sum = sum + v
        end
        return "表所有元素的和为 " .. sum
    end
})

print(mytable)          --> 表所有元素的和为 60
```

从本文中我们可以知道元表可以很好的简化我们的代码功能，所以了解 Lua 的元表，可以让我们写出更加简单优秀的 Lua 代码。
