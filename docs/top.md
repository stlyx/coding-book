# top

top 命令用于实时显示系统当前运行状态的详细信息，包括 CPU 使用率、内存使用情况、进程列表等。

## 基本用法

大部分 linux 发行版都会自带 top 命令，可以直接使用。如果没有可以尝试通过 `sudo yum install -y procps` 安装。

通常使用 `top -c`，`-c` 显示完整启动命令。回显如下：

```log
top - 17:57:20 up  5:01,  1 user,  load average: 1.82, 1.38, 1.24
Tasks: 661 total,   2 running, 659 sleeping,   0 stopped,   0 zombie
%Cpu(s):  3.7 us,  3.2 sy,  0.0 ni, 91.9 id,  0.2 wa,  0.6 hi,  0.5 si,  0.0 st
MiB Mem :  31558.3 total,    298.4 free,   7051.3 used,  24208.7 buff/cache    
MiB Swap:      0.0 total,      0.0 free,      0.0 used.  24075.1 avail Mem 

   PID USER      PR  NI    VIRT    RES    SHR S  %CPU  %MEM     TIME+ COMMAND
 33588 root      20   0 1375392 164572  52984 S  16.2   0.5  36:07.01 /usr/local/bin/kubelet --conf+
 34413 root      20   0 2191352 921748  64236 S   8.6   2.9  39:02.20 /usr/local/bin/kube-apiserver+
```

以下是回显中各个参数的解释：

- Summary 部分
  - 开机时间与负载
    - **17:57:20** - 当前系统时间
    - **up 5:01** - 系统已经运行的时间
    - **1 user** - 当前登录系统的用户数量
    - **load average: 1.82, 1.38, 1.24** - 系统的平均负载，分别是过去 1 分钟、5 分钟和 15 分钟的平均值
  - 任务和 CPU 状态
    - **Tasks: 661 total** - 当前系统中总共的任务数量
    - **2 running** - 当前正在运行的任务数量
    - **659 sleeping** - 当前处于休眠状态的任务数量
    - **0 stopped** - 当前已经停止的任务数量
    - **0 zombie** - 当前的僵尸进程数量
    - **%Cpu(s):** - CPU 的使用情况：
      - **3.7 us** (user space) - 用户空间占用的 CPU 百分比
      - **3.2 sy** (system space) - 内核空间占用的 CPU 百分比
      - **0.0 ni** (nice) - 通过“nice”调整优先级后的进程占用的 CPU 百分比
      - **91.9 id** (idle) - 空闲时间占用的 CPU 百分比
      - **0.2 wa** (iowait) - 等待 I/O 操作完成占用的 CPU 百分比
      - **0.6 hi** (hardware interrupts) - 硬中断占用的 CPU 百分比
      - **0.5 si** (software interrupts) - 软中断占用的 CPU 百分比
      - **0.0 st** (steal time) - 虚拟机被其他进程占用的 CPU 时间百分比
  - 内存占用
    - **MiB Mem:** - 内存使用情况：
      - **31558.3 total** - 总内存
      - **298.4 free** - 可用内存
      - **7051.3 used** - 已使用内存
      - **24208.7 buff/cache** - 缓存和缓冲区占用的内存
    - **MiB Swap:** - 交换区使用情况：
      - **0.0 total** - 总交换区大小
      - **0.0 free** - 可用交换区大小
      - **0.0 used** - 已使用交换区大小
      - **24075.1 avail Mem** - 可用内存大小
- 任务列表
  - **PID** - 进程 ID
  - **USER** - 进程所属的用户
  - **PR** - 进程优先级
  - **NI** - Nice 值，影响进程优先级
  - **VIRT** - 进程占用的虚拟内存总量
  - **RES** - 进程占用的常驻内存
  - **SHR** - 进程占用的共享内存
  - **S** - 进程状态，以下是可能的值：
    - **R** - 运行中
    - **S** - 休眠中
    - **D** - 不可中断的休眠状态
    - **T** - 停止或被追踪状态
    - **Z** - 僵尸进程
  - **%CPU** - 进程占用的 CPU 百分比
  - **%MEM** - 进程占用的内存百分比
  - **TIME+** - 进程运行的累计时间（格式为 分：秒）
  - **COMMAND** - 启动进程的命令

## 常用交互操作

在 `top` 命令的交互界面下，你可以使用一些快捷键来调整显示内容和排序方式：

- `h`: 显示帮助信息
- `q`: 退出 `top`
- `P`: 按 CPU 使用率排序
- `M`: 按内存使用率排序
- `T`: 按累计 CPU 时间排序
- `1`: 显示所有 CPU 核心的信息
- `k`: 终止一个进程。需要输入 PID
- `r`: 改变进程的优先级（renice）。需要输入 PID
- `s`: 修改刷新时间间隔
- `N`: 按 PID 排序
- `f`: 修改显示列

## 命令行参数

- `-H` 可以展示进程中每个线程的 CPU 占用。
- `-d seconds`: 刷新间隔时间（单位：秒）。
- `-p pid`: 仅监视指定的进程。

## 完整文档

top 的完整手册可以通过 `man top` 查看，或者 <https://man7.org/linux/man-pages/man1/top.1.html>。

其中有一段 Linux 内存类型的描述，摘录如下：

> ### Linux Memory Types | Linux 内存类型
> 
> For our purposes there are three types of memory, and one is
> optional. First is physical memory, a limited resource where
> code and data must reside when executed or referenced. Next is
> the optional swap file, where modified (dirty) memory can be
> saved and later retrieved if too many demands are made on
> physical memory. Lastly we have virtual memory, a nearly
> unlimited resource serving the following goals:
> 
> 为了便于理解，我们可以将内存分为三种类型，其中一种是可选的。首先是物理内存，这是代码和数据在执行或引用时必须驻留的有限资源。其次是可选的交换文件（swap file），用于保存修改过的（脏）内存，以便在物理内存需求过高时可以恢复使用。最后是虚拟内存，它几乎无限，旨在实现以下目标：
> 
> 1. abstraction, free from physical memory addresses/limits
> 2. isolation, every process in a separate address space
> 3. sharing, a single mapping can serve multiple needs
> 4. flexibility, assign a virtual address to a file
> 
> <p/>
> 
> 1. 抽象化，摆脱物理内存地址和限制。
> 2. 隔离，每个进程拥有独立的地址空间。
> 3. 共享，单个映射可以满足多种需求。
> 4. 灵活性，可以将虚拟地址分配给文件。
> 
> Regardless of which of these forms memory may take, all are
> managed as pages (typically 4096 bytes) but expressed by default
> in top as KiB (kibibyte). The memory discussed under topic ‘2c.
> MEMORY Usage’ deals with physical memory and the swap file for
> the system as a whole. The memory reviewed in topic ‘3. FIELDS /
> Columns Display’ embraces all three memory types, but for
> individual processes.
> 
> 无论采取哪种形式，所有内存都以页（通常 4096 字节）的形式进行管理，默认情况下在 `top` 命令中以 KiB（千字节）表示。主题“2c. MEMORY Usage”讨论的是整个系统的物理内存和交换文件。而主题“3. FIELDS / Columns Display”涵盖了所有三种内存类型，但针对的是各个进程。
> 
> For each such process, every memory page is restricted to a
> single quadrant from the table below. Both physical memory and
> virtual memory can include any of the four, while the swap file
> only includes #1 through #3. The memory in quadrant #4, when
> modified, acts as its own dedicated swap file.
> 
> 对于每个这样的进程，每一页内存都被限制在下表中的一个象限内。物理内存和虚拟内存可以包括所有四个象限，而交换文件仅包括前三个象限。第四象限的内存，一旦被修改，就会充当其自身的专用交换文件。
> 
>                                Private | Shared
>                            1           |          2
>       Anonymous  . stack               |
>                  . malloc()            |
>                  . brk()/sbrk()        | . POSIX shm*
>                  . mmap(PRIVATE, ANON) | . mmap(SHARED, ANON)
>                 -----------------------+----------------------
>                  . mmap(PRIVATE, fd)   | . mmap(SHARED, fd)
>     File-backed  . pgms/shared libs    |
>                            3           |          4
> 
> The following may help in interpreting process level memory
> values displayed as scalable columns and discussed under topic
> ‘3a. DESCRIPTIONS of Fields’.
> 
> 以下内容有助于解释在可缩放列中显示的进程级内存值，这些值在主题“3a. DESCRIPTIONS of Fields”中进行了讨论。
> 
>     %MEM - simply RES divided by total physical memory
>     CODE - the ‘pgms’ portion of quadrant 3
>     DATA - the entire quadrant 1 portion of VIRT plus all
>            explicit mmap file-backed pages of quadrant 3
>     RES  - anything occupying physical memory which, beginning with
>            Linux-4.5, is the sum of the following three fields:
>            RSan - quadrant 1 pages, which include any
>                   former quadrant 3 pages if modified
>            RSfd - quadrant 3 and quadrant 4 pages
>            RSsh - quadrant 2 pages
>     RSlk - subset of RES which cannot be swapped out (any quadrant)
>     SHR  - subset of RES (excludes 1, includes all 2 & 4, some 3)
>     SWAP - potentially any quadrant except 4
>     USED - simply the sum of RES and SWAP
>     VIRT - everything in-use and/or reserved (all quadrants)
> 
> - `%MEM` - 仅仅是 `RES` 除以总物理内存
> - `CODE` - 第三象限的“程序”部分
> - `DATA` - 第一象限中的整个 `VIRT` 部分加上第三象限中所有显式 mmap 文件映射页面
> - `RES` - 占用物理内存的任何内容，从 Linux-4.5 开始，它是以下三个字段的总和：
>     - `RSan` - 第一象限的页面，包括任何被修改的前象限 3 的页面
>     - `RSfd` - 第三象限和第四象限的页面
>     - `RSsh` - 第二象限的页面
> - `RSlk` - `RES` 的子集，不能被交换出去（任何象限）。
> - `SHR` - `RES` 的子集（排除第一象限，包含第二和第四象限的全部以及第三象限的一部分）。
> - `SWAP` - 可能是除了第四之外的任何象限。
> - `USED` - `RES` 和 `SWAP` 的总和。
> - `VIRT` - 所有正在使用或预留的内存（所有象限）。
> 
> **Note:** Even though program images and shared libraries are
> considered private to a process, they will be accounted for as
> shared (SHR) by the kernel.
> 
> **注意：** 即使程序映像和共享库被认为是进程的私有部分，它们也将被内核计为共享（`SHR`）。
