## Lua 协同程序 coroutine

### 一、协同 (coroutine)
**协同程序** 与 **线程** 比较类似：拥有独立的堆栈，独立的局部变量，独立的指令指针，同时又与其它协同程序共享全局变量和其它大部分东西。

协同是非常强大的功能，但是用起来也很复杂。

#### 线程和协同程序区别
- 一个具有多个线程的程序可以同时运行几个线程，而协同程序却需要彼此协作的运行。
- 在任一指定时刻只有一个协同程序在运行，并且这个正在运行的协同程序只有在明确的被要求挂起的时候才会被挂起。
- 协同程序有点类似同步的多线程，在等待同一个线程锁的几个线程有点类似协同。

### 二、方法
| 方法                                 | 作用描述                                                                                                                              |
| ------------------------------------ | ------------------------------------------------------------------------------------------------------------------------------------- |
| `coroutine.create(f)`                | 使用函数 `f` 创建一个新的协同程序，并返回 `thread` 类型的对象。                                                                       |
| `coroutine.resume(co [, val1, ...])` | 恢复（重启）协程 `co` 并传递参数(如果有的话)。它返回操作状态和可选的其他返回值。                                                      |
| `coroutine.running()`                | 返回正在跑的coroutine，当使用 `running` 的时候，就是返回一个corouting的线程号。                                                       |
| `coroutine.status(co)`               | 查看coroutine的状态，有 `dead`，`suspend`，`running` 三种。                                                                           |
| `coroutine.wrap(f)`                  | 与 `create` 一样，`coroutine.wrap` 函数会创建一个协同程序，但它不会返回协同程序本身，而是返回一个函数，当调用它时，它会恢复协同程序。 |
| `coroutine.yield(...)`               | 挂起coroutine，暂停正在运行的协同程序。传递给此方法的参数充当 `resume` 函数的附加返回值。                                             |

### 三、示例
#### 示例1
```lua
-- coroutine_test.lua 文件
co = coroutine.create(function(i)
    print(i);
end)

coroutine.resume(co, 1)             --> 1
print(coroutine.status(co))         --> dead（死亡）

print("----------")

co = coroutine.wrap(function(i)
    print(i);
end)
 
-- 函数调用
co(10)                               --> 10

print("----------")
 
co2 = coroutine.create(function()
    for i=1,10 do
        print(i)
        if i == 3 then
            print(coroutine.status(co2))    --> running（运行）
            print(coroutine.running())      --> thread: xxxxxx   false
        end
        coroutine.yield()
    end
end)

coroutine.resume(co2)               --> 1
coroutine.resume(co2)               --> 2
coroutine.resume(co2)               --> 3
 
print(coroutine.status(co2))        --> suspended（挂起）
print(coroutine.running())          --> thread: xxxxxx    true

print("----------")
```

执行结果
```
1
dead
----------
10
----------
1
2
3
running
thread: 0x1c49fd8	false
suspended
thread: 0x1c3d018	true
----------
```

如果将 `coroutine.yield()` 一行注释掉，执行结果如下
```
1
dead
----------
10
----------
1
2
3
running
thread: 0x7fe6f1505258	false
4
5
6
7
8
9
10
dead
thread: 0x7fe6f2000008	true
----------
```

`coroutine.running` 就可以看出来 `coroutine` 在底层实现就是一个线程。

- 当 `create` 一个 coroutine 的时候就是在新线程中注册了一个事件。
- 当使用 `resume` 触发事件的时候，`create` 的 coroutine 函数就被执行了
- 当遇到 `yield` 的时候就代表挂起当前线程，等候再次 `resume` 触发事件。

#### 示例2
接下来我们分析一个更详细的实例：
```lua
function foo (a)
    print("foo 函数输出", a)
    return coroutine.yield(2 * a)   -- 返回  2*a 的值
end
 
co = coroutine.create(function (a, b)
    print("第一次协同程序执行输出", a, b)
    local r = foo(a + 1)
     
    print("第二次协同程序执行输出", r)
    local r, s = coroutine.yield(a+b, a-b)  -- a，b 的值为第一次调用协同程序时传入
     
    print("第三次协同程序执行输出", r, s)
    return b, "结束协同程序"                   -- b的值为第二次调用协同程序时传入
end)
        
print("main", coroutine.resume(co, 1, 10))
print()
print("main", coroutine.resume(co, "r"))
print()
print("main", coroutine.resume(co, "x", "y"))
print()
print("main", coroutine.resume(co, "x", "y")) 

```
执行结果
```
第一次协同程序执行输出	1	10
foo 函数输出	2
main	true	4

第二次协同程序执行输出	r
main	true	11	-9

第三次协同程序执行输出	x	y
main	true	10	结束协同程序

main	false	cannot resume dead coroutine
```

分析
- 第一次 `resume`，将协同程序唤醒，`resume` 操作成功返回 `true`。
- 协同程序运行，运行到 `yield` 挂起协同程序，第一次 `resume` 返回（此处 yield 返回，参数是 resume 的参数）。
- 第二次 `resume`，再次唤醒协同程序（此处resume的参数中，除了第一个参数，剩下的参数将作为yield的参数）。
- 协同程序运行，运行到 `yield` 挂起协同程序，第二次 `resume` 返回。
- 协同程序继续运行，重复如上步骤。
- 如果协同程序运行完成后，继续调用 `resume` 方法则输出：`cannot resume dead coroutine`

`resume` 和 `yield` 的配合强大之处在于
- `resume` 处于主程中，它将外部状态（数据）传入到协同程序内部。
- `yield` 则将内部的状态（数据）返回到主程中。

### 四、生产者-消费者问题
使用 Lua 的协同程序来完成生产者-消费者这一经典问题。
```lua
local newProductor

function productor()
     local i = 0
     while true do
          i = i + 1
          send(i)     -- 将生产的物品发送给消费者
     end
end

function consumer()
     while true do
          local i = receive()     -- 从生产者那里得到物品
          print('消费者', i)
     end
end

function receive()
     local status, value = coroutine.resume(newProductor)
     return value
end

function send(x)
     coroutine.yield(x)     -- x表示需要发送的值，值返回以后，就挂起该协同程序
end

-- 启动程序
newProductor = coroutine.create(productor)
consumer()
```
以上实例执行输出结果为：
```
1
2
3
4
5
6
7
8
9
10
11
12
13
……
```

通常会将协同程序与多路程序设计语言的线程进行比较，但需要了解协同程序具有类似的线程功能，但协同程序一次只执行一个程序，并且永远不会同时执行。

通过暂时保留某些信息来控制程序执行顺序以满足需求。使用带有协同程序的全局变量为协同程序提供了更大的灵活性。
