# 使用GDB调试程序

1. [Debugging with GDB](https://sourceware.org/gdb/onlinedocs/gdb/index.html)
2. GDB基本命令

| 命令    | 说明    |
| --    |   --  |
|break(b)[文件名:]行号  | 在某个文件某一行设置断点   |
|break [文件名:]函数名   | 在某个文件某个函数开头设置断点   |
|break...if...   | 设置条件断点   |
|break *address   | 设置地址断点   |
|watch expression   | 当expression值发生变化时，停止执行程序   |
|step(s)   | 单步执行，如有函数调用则进入函数中   |
|next(n)   | 单步执行，跳过函数条用   |
|continue(c)   | 恢复执行   |
|backtrace(bt)   | 查看函数调用堆栈   |
|frame(f) 帧编号   | 选择栈帧   |
|finish   | 执行到当前函数返回，然后停下来等待   |
|start   | 开始执行程序，停在第一行语句  |
|run(r)   | 从头开始执行程序   |
|info(i) locals   | 查看当前栈帧局部变量的值   |
|info(i) display   | 查看display信息   |
|info(i) breakpoints(b)   | 查看断点和观察点信息   |
|disable breakpoints   | 禁用断点，可以根据断点号设置   |
|enable breakpoints   | 启用断点   |
|delete breakpoints   | 删除断点   |
|display 变量名   | 跟踪查看一个变量，每次停下来都显式它的值   |
|undisplay   | 取消跟踪变量   |
|print 变量名   | 打印变量值   |
|set var=value   | 修改变量值   |
|list(l)   | 列出源代码，每次10行   |
|list 行号   | 列出该行前后的源代码  |
|list 函数名   | 列出函数定义前后的源代码   |
|list *address   | 列出含有地址address附近的源代码   |

3. 为GDB设置参数
    1. `gdb a.out --args args`
    2. 在gdb中`run`之前执行`set args`(同理，可使用show args显式参数)
    3. 在gdb中`run`命令后添加args
4. gdb显式数组

    ```
    p (int [10])*a
    或
    p *a@10
    ```
5. 调试宏
    1. 在编译时加上`-ggdb3`就可以调试宏了
    2. `info macro`：可查看宏在哪些文件中被引用了
    3. `macro`：可以查看宏展开的样子
6. 源文件
    1. 编译时加上`-g`参数
    2. 使用`directory`命令指定源代码目录
7. `x`命令: 格式：`x /nfu <addr>`，`x`是examine的缩写，`n`表示要显示的内存单元的个数，f表示显式格式，`u`表示一个内存单元的长度
    1. `f`取值：
        * `x`：十六进制
        * `d`：十进制
        * `u`：十进制格式显式无符号整型
        * `o`：八进制
        * `t`：二进制
        * `a`：十六进制
        * `i`：指令格式（显式反汇编）
        * `c`：字符格式
        * `f`：浮点数格式
    2. `u`表示一个地址单元的长度
        * `b`：单字节
        * `h`：双字节
        * `w`：四字节
        * `g`：八字节
