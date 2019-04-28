## Lua MySQL操作
对于简单的数据操作，可使用文件处理。但有时，这些文件操作可能效率不高，可扩展且功能强大，为此经常需要使用数据库。

### 一、LuaSQL 介绍
LuaSQL 是一个从 Lua 到 DBMS 的简单接口。 它使Lua程序能够：

- 连接到 ODBC，ADO，Oracle，MySQL，SQLite，Firebird 和 PostgreSQL 数据库;
- 执行任意SQL语句;
- 以逐行光标方式检索结果。

LuaSQL是免费软件，使用与Lua 5.1相同的许可证。

LuaSQL 可以使用 LuaRocks 来安装可以根据需要安装你需要的数据库驱动。

- LuaRocks 官网：https://luarocks.org/
- LuaSQL 官网：http://keplerproject.github.io/luasql/
- LuaSQL GitHub：https://github.com/keplerproject/luasql

### 二、LuaRocks 安装

#### Linux 环境
```bash
# 下载
$ wget http://luarocks.org/releases/luarocks-2.2.1.tar.gz
# 解压
$ tar zxpf luarocks-2.2.1.tar.gz
# 进入
$ cd luarocks-2.2.1
# 编译
$ ./configure; sudo make bootstrap
# 安装
$ sudo luarocks install luasocket

# lua 命令行
$ lua
Lua 5.3.0 Copyright (C) 1994-2015 Lua.org, PUC-Rio
> require "socket"
> 
```

#### Window 环境
Window 下安装 LuaRocks：https://github.com/keplerproject/luarocks/wiki/Installation-instructions-for-Windows

### 三、安装不同数据库驱动：
```bash
luarocks install luasql-sqlite3
luarocks install luasql-postgres
luarocks install luasql-mysql
luarocks install luasql-sqlite
luarocks install luasql-odbc
```

你也可以使用源码安装方式，Lua Github 源码地址：https://github.com/keplerproject/luasql

### 四、Lua 连接MySql 数据库：
```lua
require "luasql.mysql"

--创建环境对象
env = luasql.mysql()

--连接数据库
conn = env:connect("erp","yklyx","99ykTour!99YingK","192.168.128.36",3306)

--设置数据库的编码格式
conn:execute"SET NAMES UTF8"

--执行数据库操作
cur = conn:execute("select * from bom_applied")

row = cur:fetch({}, "a")

--文件对象的创建
file = io.open("role.txt","w+");

while row do
    var = string.format("%d %s\n", row.id, row.name)

    print(var)

    file:write(var)

    row = cur:fetch(row,"a")
end

file:close()  --关闭文件对象
conn:close()  --关闭数据库连接
env:close()   --关闭数据库环境
```
