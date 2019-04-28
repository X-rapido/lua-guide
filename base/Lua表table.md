## Lua 表 table

- table 是 Lua 的一种数据结构，用来帮助我们创建不同的数据类型，如：数字、字典等。
- table 使用关联型数组，你可以用任意类型的值来作数组的索引，但这个值不能是 `nil`。
- table 是不固定大小的，你可以根据自己需要进行扩容。
- table 可以解决模块（module）、包（package）和对象（Object）的表示。例如 `string.format` 表示使用 format 来索引 table `string`。

### 一、table(表)的构造
构造器是创建和初始化表的表达式。表是 Lua 特有的功能强大的东西。最简单的构造函数是 `{}`，用来创建一个空表。可以直接初始化数组。
```lua
-- 初始化表
mytable = {}

-- 指定值
mytable[1]= "Lua"

-- 移除引用
mytable = nil
-- lua 垃圾回收会释放内存
```

当我们为 table `a` 并设置元素，然后将 `a` 赋值给 `b`，则 `a` 与 `b` 都指向同一个内存。如果 `a` 设置为 `nil`，则 `b` 同样能访问 table 的元素。当没有对表的引用时，Lua的垃圾回收机制会清理相对应的内存。

```lua
-- 简单的 table
mytable = {}
print("mytable 的类型是 ",type(mytable))                        --> mytable 的类型是 	table

mytable[1]= "Lua"
mytable["wow"] = "修改前"
print("mytable 索引为 1 的元素是 ", mytable[1])                  --> mytable 索引为 1 的元素是 	Lua
print("mytable 索引为 wow 的元素是 ", mytable["wow"])            --> mytable 索引为 wow 的元素是 	修改前

-- alternatetable和mytable的是指同一个 table
alternatetable = mytable

print("alternatetable 索引为 1 的元素是 ", alternatetable[1])    --> mytable 索引为 1 的元素是 	Lua
print("mytable 索引为 wow 的元素是 ", alternatetable["wow"])     --> mytable 索引为 wow 的元素是 	修改前

alternatetable["wow"] = "修改后"
print("mytable 索引为 wow 的元素是 ", mytable["wow"])            --> mytable 索引为 wow 的元素是 	修改后

-- 释放变量
alternatetable = nil
print("alternatetable 是 ", alternatetable)                    --> alternatetable 是 	nil

-- mytable 仍然可以访问
print("mytable 索引为 wow 的元素是 ", mytable["wow"])            --> mytable 索引为 wow 的元素是 	修改后

mytable = nil
print("mytable 是 ", mytable)                                  --> mytable 是 	nil
```

### 二、Table 操作

#### （1）table.concat (table [, step [, start [, end]]])：连接
根据给定的参数连接表中的字符串，`table.concat()` 列出参数中指定 table 数组部分从 `start` 位置到 `end` 位置的所有元素，元素间以指定的分隔符 `step` 隔开。

```lua
fruits = {"banana","orange","apple"}
-- 返回 table 连接后的字符串
print("连接后的字符串 ",table.concat(fruits))               --> 连接后的字符串 	bananaorangeapple

-- 指定连接字符
print("连接后的字符串 ",table.concat(fruits,", "))          --> 连接后的字符串 	banana, orange, apple

-- 指定索引来连接 table
print("连接后的字符串 ",table.concat(fruits,", ", 2,3))     --> 连接后的字符串 	orange, apple
```

#### （2）table.insert (table, [pos,] value)：插入
在 table 的数组部分指定位置 `pos` 插入一个值为 `value` 的元素，`pos` 参数可选, 默认为数组部分末尾。

```lua
fruits = {"banana","orange","apple"}

-- 在末尾插入
table.insert(fruits, "mango")
print("索引为 4 的元素为 ",fruits[4])           --> 索引为 4 的元素为 	mango

-- 在索引为 2 的键处插入
table.insert(fruits,2, "grapes")
print("索引为 2 的元素为 ", fruits[2])          --> 索引为 2 的元素为 	grapes

print("最后一个元素为 ", fruits[5])             --> 最后一个元素为 	mango

-- 在末尾移除
table.remove(fruits)
print("移除后最后一个元素为 ", fruits[5])
```

#### （3）table.maxn (table)：最大key值
指定 table 中所有正数 key 值中最大的 key 值，如果不存在 key 值为正数的元素, 则返回 `0`。

Lua5.2 之后该方法已经不存在了，本文使用了自定义函数实现
```lua
function table_maxn(t)
    local mn = 0
    for k, v in pairs(t) do
        if mn < k then
            mn = k
        end
    end
    return mn
end
tbl = {[1] = "a", [2] = "b", [3] = "c", [26] = "z"}
print("tbl 长度 ", #tbl)                        --> tbl 长度 	3
print("tbl 最大值 ", table_maxn(tbl))           --> tbl 最大值 	26
```

#### （4）table.remove (table [, pos])：移除
返回 table 数组部分位于 `pos` 位置的元素，其后的元素会被前移，`pos`参数可选, 默认为 table 长度, 即从最后一个元素删起。

#### （5）table.sort (table [, comp])：排序
根据可选的比较器参数 `comp` 对表进行升序排序。

```lua
fruits = {"banana","orange","apple","grapes"}

for k,v in ipairs(fruits) do
   print(k,v)
end

table.sort(fruits)
print("排序")

for k,v in ipairs(fruits) do
   print(k,v)
end
```






