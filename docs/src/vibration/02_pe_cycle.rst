P/E Cycle
=========

.. code-block:: shell

    ; size -- 控制涉及的设备地址范围, 应该设为100%
    ; numjobs -- 单线程或多线程
    ; loops -- 循环次数
    ; example -- sudo -S filename=/dev/sda size=100% numjobs=1 loops=1 fio /home/liudeyuan/Desktop/vibration/02_pe_cycle < /home/liudeyuan/Desktop/vibration/password
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

刷P/E Cycle的过程和Retention测试的相同，在此不再赘述，再次强调：

- **脚本中的对应的设备信息一定要记得改，也千万不要改错，否则会很惨烈**
- **在进行操作之前，请一定进行unmount，确定设备处于unmount状态**
- **一般来说，/dev/nvme0n1和/dev/sda为系统盘，请千万不要向这两个盘写入数据，否则很可能造成不可恢复的数据丢失，以防万一，可以先备份一下重要数据**
