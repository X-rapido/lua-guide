## Lua 字符串
字符串是由一系列字符以及控制字符组成，如换页符。 字符串可以用三种形式初始化，包括
- 单引号之间的字符
- 双引号之间的字符
- `[[` 和 `]]`之间的字符

```lua
string1 = "Lua"
print("\"String 1 is\"", string1)

string2 = '程序喵'
print("String 2 is", string2)
 
string3 = [["Java 天下第一"]]
print("String 3 is",string3)
```

当运行上面的程序时，将得到以下输出
```
"String 1 is"	Lua
String 2 is	程序喵
String 3 is	"Java 天下第一"

```
转义字符用于表示不能直接显示的字符，比如后退键，回车键，等。如在字符串转换双引号可以使用 "`\"`"。

### 一、转义字符对应关系

| 转义字符 | 意义                                | ASCII码值（十进制） |
| -------- | ----------------------------------- | ------------------- |
| `\a`     | 响铃(BEL)                           | `007`               |
| `\b`     | 退格(BS) ，将当前位置移到前一列     | `008`               |
| `\f`     | 换页(FF)，将当前位置移到下页开头    | `012`               |
| `\n`     | 换行(LF) ，将当前位置移到下一行开头 | `010`               |
| `\r`     | 回车(CR) ，将当前位置移到本行开头   | `013`               |
| `\t`     | 水平制表(HT) （跳到下一个TAB位置）  | `009`               |
| `\v`     | 垂直制表(VT)                        | `011`               |
| `\\`     | 代表一个反斜线字符'`'\'`            | `092`               |
| `\'`     | 代表一个单引号（撇号）字符          | `039`               |
| `\"`     | 代表一个双引号字符                  | `034`               |
|          | 空字符(NULL)                        | `000`               |
| `\ddd`   | 1到3位八进制数所代表的任意字符      | 三位八进制          |
| `\xhh`   | 1到2位十六进制所代表的任意字符      | 二位十六进制        |

### 二、字符串操作
Lua 提供了很多的方法来支持字符串的操作：
#### （1）string.upper(argument)：字符串全部大写字母。
```lua
print(string.upper("Java"))        --> JAVA
```

#### （2）string.lower(argument)：字符串全部小写字母。
```lua
print(string.lower("Java"))        --> java
```

#### （3）string.gsub(mainString, findString, replaceString, num)：字符串中替换
- `mainString`：要替换的字符
- `findString`：被替换的字符
- `replaceString`：要替换的字符
- `num`：替换次数（忽略则全部替换）

多返回值结果
```lua
print(string.gsub("aaaa", "a", "z", 3));        --> zzza	3
```

#### （4）string.find(str, substr, [init, [end]])：字符搜索
在一个指定的目标字符串中搜索指定的内容(第三个参数为索引)，返回其具体位置。不存在则返回 `nil`。
```lua
print(string.find("Hello Lua user", "Lua", 1))      --> 
```

#### （5）string.reverse(arg)：字符翻转
字符串反转
```lua
print(string.reverse("Java"))           --> avaJ
```

#### （6）string.format(...)：格式化
一个类似 `printf` 的格式化字符串
```lua
print(string.format("the value is：%d", 4))                      --> the value is：4
print(string.format("字符串格式化 %s %s", "Hello ", "World"))      --> 字符串格式化 Hello  World
print(string.format("%.4f", 1/3))                                --> 0.3333

date = 2; month = 1; year = 2019
print(string.format("日期格式化 %02d/%02d/%03d", date, month, year))    --> 日期格式化 02/01/2019
```

#### （7）string.char(arg) 和 string.byte(arg[,int])：字符和字节表示
- char 将整型数字转成字符并连接
- byte 转换字符为整数值(可以指定某个字符，默认第一个字符)

```lua
-- 转换第一个字符
print(string.byte("Lua"))               --> 76
-- 转换第三个字符
print(string.byte("Lua", 3))            --> 97
-- 转换末尾第一个字符
print(string.byte("Lua", -1))           --> 97
-- 第二个字符
print(string.byte("Lua", 2))            --> 117
-- 转换末尾第二个字符
print(string.byte("Lua", -2))           --> 117

-- 整数 ASCII 码转换为字符
print(string.char(97))                  --> a
print(string.char(97, 98, 99, 100))     --> abcd
```

#### （8）string.len(arg)：字符串长度
计算字符串长度
- 一个字母、数字各占 1 个长度
- 一个汉字占 3 个长度
- 一个表情占 4 个字符
```lua
print(string.len("chengxumiao"))       --> 11
print(string.len("程序喵"))             --> 9
print(string.len("🐱"))                --> 4
```

#### （9）string.rep(string, n)：字符串拷贝
返回字符串string的n个拷贝
```lua
print(string.rep("Java", 3))        --> JavaJavaJava
```

#### （10）.. ：字符串拼接
```lua
print("www.ibloger" .. ".net")      --> www.ibloger.net
print("程".."序".."喵".."🐱")        --> 程序喵🐱
```
