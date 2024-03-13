## Lab1

[实验页面](https://pdos.csail.mit.edu/6.828/2021/labs/util.html)

**基本命令**

启动 `xv6` ：`make qemu`

查看进程信息：`Ctrl-p`

---

**sleep**

Q. 在 xv6 操作系统中实现一个类似 UNIX 中的 `sleep` 程序，让程序暂停指定数量的 ticks。每个 tick 是由 xv6 内核定义的时间概念，即来自定时器芯片的两次中断之间的时间。你可以将以下代码添加到 `user/sleep.c` 文件中以实现 `sleep` 函数：

> `sleep 10`个人感觉近似 1s

由于 `user.h` 已经为我们写好了系统调用 `int sleep(int)` 因此，我们只需要使用该接口来实现 `sleep`，然后注意当输入参数错误时的输出即可：

通过观察发现：

* `fprintf()` 将文件描述符 `2` 视为 `stderr` ，`1` 视为 `stdout`，`0` 视为 `stdin`
* `exit(0)` ：正确退出，`exit(1)` 错误退出。
* 其提供的大部分程序对于参数都没有考虑会溢出整型，因此我也不考虑。

```c
#include "kernel/types.h" // 系统调用相关的类型定义
#include "kernel/stat.h" 
#include "user/user.h"

int main(int args, char* argv[]) {
        if (args !=  2) {
                fprintf(2, "usage: sleep <times>\n");
                exit(1);
        }

        int num = atoi(argv[1]);
        sleep(num);
        exit(0);
}
```

在仓库主目录下使用 `make grade` 得到：

```bash
== Test sleep, no arguments ==
$ make qemu-gdb
sleep, no arguments: OK (1.2s)
== Test sleep, returns ==
$ make qemu-gdb
sleep, returns: OK (0.3s)
== Test sleep, makes syscall ==
$ make qemu-gdb
sleep, makes syscall: OK (0.9s)
```

