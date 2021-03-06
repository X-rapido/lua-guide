## Lua 运算符
运算符是告诉解释器执行特定数学或逻辑操作的符号。 Lua语言内置运算符丰富，并提供以下类型的运算符。

- 算术运算符
- 关系运算符
- 逻辑运算符
- 其它运算符

### 一、算术运算符
下表列出了 Lua 语言中的常用算术运算符，设定 A 的值为10，B 的值为 20。

| 操作符 | 描述 | 实例          |
| ------ | ---- | ------------- |
| `+`    | 加法 | `A + B = 30`  |
| `-`    | 减法 | `A - B = -10` |
| `*`    | 乘法 | `A * B = 200` |
| `/`    | 除法 | `B / A = 2`   |
| `%`    | 取余 | `B % A = 0`   |
| `^`    | 乘幂 | `A^2 = 100`   |
| `-`    | 负号 | `-A = -10`    |

### 二、关系运算符
下表列出了 Lua 语言中的常用关系运算符，设定 A 的值为10，B 的值为 20。

| 操作符 | 描述                                                               | 实例                  |
| ------ | ------------------------------------------------------------------ | --------------------- |
| `==`   | 等于，检测两个值是否相等，相等返回 true，否则返回 false            | (A == B) 为 false。   |
| `~=`   | 不等于，检测两个值是否相等，相等返回 false，否则返回 true          | (A ~= B) 为 true。    |
| `>`    | 大于，如果左边的值大于右边的值，返回 true，否则返回 false          | (A > B) 为 false。    |
| `<`    | 小于，如果左边的值大于右边的值，返回 false，否则返回 true          | (A < B) 为 true。     |
| `>=`   | 大于等于，如果左边的值大于等于右边的值，返回 true，否则返回 false  | (A >= B) is not true. |
| `<=`   | 小于等于， 如果左边的值小于等于右边的值，返回 true，否则返回 false | (A <= B) is true.     |


### 三、逻辑运算符
下表列出了 Lua 语言中的常用逻辑运算符，设定 A 的值为 true，B 的值为 false。

| 操作符 | 描述                                                                | 实例                   |
| ------ | ------------------------------------------------------------------- | ---------------------- |
| `and`  | 逻辑与操作符。 如果两边的操作都为 true 则条件为 true。              | (A and B) 为 false。   |
| `or`   | 逻辑或操作符。 如果两边的操作任一一个为 true 则条件为 true。        | (A or B) 为 true。     |
| `not`  | 逻辑非操作符。与逻辑运算结果相反，如果条件为 true，逻辑非为 false。 | not(A and B) 为 true。 |

### 四、其他运算符
下表列出了 Lua 语言中的连接运算符与计算表或字符串长度的运算符：


| 操作符 | 描述                               | 实例                                                                  |
| ------ | ---------------------------------- | --------------------------------------------------------------------- |
| `..`   | 连接两个字符串                     | `a..b` ，其中 a 为 "Hello "，b 为 "World", 输出结果为 "Hello World"。 |
| `#`    | 一元运算符，返回字符串或表的长度。 | `#"Hello"` 返回 5                                                     |


### 五、运算符优先级

从高到低的顺序：
```
^
not    - (unary)
*      /
+      -
..
<      >      <=     >=     ~=     ==
and
or
```

除了 `^` 和 `..` 外所有的二元运算符都是左连接的。

```lua
a+i < b/2+1          -->       (a+i) < ((b/2)+1)
5+x^2*8              -->       5+((x^2)*8)
a < y and y <= z     -->       (a < y) and (y <= z)
-x^2                 -->       -(x^2)
x^y^z                -->       x^(y^z)
```

示例
```lua
a = 20
b = 10
c = 15
d = 5

e = (a + b) * c / d;        -- (30 * 15) / 5
print("(a + b) * c / d 运算值为  :", e)

e = ((a + b) * c) / d;      -- (30 * 15) / 5
print("((a + b) * c) / d 运算值为 :", e)

e = (a + b) * (c / d);      -- (30) * (15/5)
print("(a + b) * (c / d) 运算值为 :", e)

e = a + (b * c) / d;        -- 20 + (150/5)
print("a + (b * c) / d 运算值为   :", e)
```

