# gdb调试多进程和多线程

## 多进程

1. `set follow-fork-mode [parent|child]`
    1. `parent`: fork之后调试父进程，子进程不受影响
    2. `child`: fork之后调试子进程，父进程不受影响
2. `set detach-on-fork [on|off]`
    1. `on`: 断开调试`follow-fork-mode`指定的进程
    2. `off`: gdb将控制父子进程，`follow-fork-mode`指定的进程将被调试，另一个进程置于暂停（suspended）状态

## 多线程

1. `info threads`: 显式当前可调式的所有线程
2. `thread ID`: 切换当前调试的线程为指定ID的线程
3. `break thread_test.c:123 thread ID`: 在指定ID的线程上设置断点
4. `thread apply ID1 ID2 command`: 在指定的一个或多个线程上执行command
5. `set scheduler-locking off|on|step`
    1. `off`: 默认值，所有线程都执行，不锁定任何线程
    2. `on`: 只执行被调试线程
    3. `step`: 在单步时，除了next过一个函数的情况（实际是设置断点然后continue的行为），只执行被调试线程
