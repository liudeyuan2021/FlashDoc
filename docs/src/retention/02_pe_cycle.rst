P/E Cycle
=========

.. code-block:: shell

    ; size -- 控制涉及的设备地址范围, 应该设为100%
    ; numjobs -- 单线程或多线程
    ; loops -- 循环次数
    ; example -- sudo -S filename=/dev/nvme1n1 size=100% numjobs=1 loops=48 fio /home/liudeyuan/Desktop/retention/02_pe_cycle < /home/liudeyuan/Desktop/retention/password
    ; 本次刷完之后: p/e cycle = 49
    [writers]
    filename=${filename}
    rw=write
    direct=1
    bs=1mb
    size=${size}
    ioengine=libaio
    iodepth=64
    thread
    numjobs=${numjobs}
    loops=${loops}

    disable_lat=1
    disable_clat=1
    disable_slat=1
    disable_bw=1

我们使用FIO刷P/E Cycle，我们需要刷指定的设备，如上述代码中设备为/dev/nvme1n1。在上述代码刷P/E Cycle之前，我的设备的P/E Cycle为1，
我想刷完整(size=100%)地刷48次(loops=48)，刷完之后P/E Cycle为49，之后我再写入的数据量大概为刷1次P/E Cycle的写入数据量(96G、384G、768G≈128G、512G、1024G)，因此该轮测试的
P/E Cycle为50。

刷P/E Cycle需要耗时很久，为了尽量加快这个过程，我们把FIO的统计信息输出关闭(disable_xxx=1)。

刷P/E Cycle需要sudo权限，为了不用每次输入密码，我把密码存到了一个文件中，使用IO重定向自动输入，虽然不安全，但是方便。

另外，我也提供了tmux自动切分多个窗口的脚本，可以开启多个窗口同时刷P/E Cycle或同时进行其它操作(如写入、读取、比较数据)：

.. code-block:: shell

    bash tmux.sh

重点
--------------

同时刷多个P/E Cycle或者同时进行其它操作(如写入、读取、比较数据)，运行对应脚本之前，我们需要先查看对应的设备信息，可运行下列命令查看设备信息，
**脚本中的对应的设备信息一定要记得改，也千万不要改错，否则会很惨烈**：

.. code-block:: shell

    sudo fdisk -l

另外，有些设备如U盘，SD卡会自动mount，可运行以下命令查看mount信息， **在进行操作之前，请一定进行unmount，确定设备处于unmount状态**：

.. code-block:: shell

    df -hl

**一般来说，/dev/nvme0n1和/dev/sda为系统盘，请千万不要向这两个盘写入数据，否则很可能造成不可恢复的数据丢失，以防万一，可以先备份一下重要数据。**
