IO Workload
===========

.. code-block:: shell

    ; filename -- 设备名
    ; rw -- 读写类型
    ; size -- 控制涉及的设备地址范围, 应该设为100%
    ; numjobs -- 单线程或多线程, 应该设为1
    ; loops -- 循环次数, 应该设为1000+
    ; runtime -- 运行时间, 应该设为3h或者48h
    ; example -- sudo filename=/dev/sdb rw=randrw size=100% numjobs=1 loops=1000 runtime=3h fio --output=fio_stats.log 01_io_workload
    [io_workload]
    filename=${filename}
    rw=${rw}
    ;rwmixwrite=50
    randseed=1130
    direct=1
    bsrange=4kb-1mb
    size=${size}
    ioengine=libaio
    iodepth=16
    thread
    numjobs=${numjobs}
    loops=${loops}
    runtime=${runtime}

    group_reporting=1
    per_job_logs=0
    write_lat_log=fio
    write_bw_log=fio
    write_iops_log=fio
    disable_clat=1
    disable_slat=1
    lat_percentiles=1
    percentile_list=1:5:10:20:30:40:50:60:70:80:90:95:99:99.5:99.9:99.95:99.99:99.999:99.9999:100

我们使用FIO进行各种各样的IO测试：

- filename指定设备名
- rw指定读写类型
- size指定读写范围
- numjobs指定读写线程数
- loops指定循环次数，因为我们控制结束的是时间信息，因此可以把loops设大一些
- runtime指定运行时间。

我们采用固定的randseed，从而保证每次读写的序列是一致的。采用direct=1，直接读写，排除文件系统的干扰。其它的数据也尽量和
论文中保持一致。

group_reporting之后的一些变量主要在指定需要记录的统计信息，我们主要记录bw、lat、iops、stats等信息。

生成运行命令
--------------

由于io_workload可以有非常多种，并且fio_stats.log只会保存在运行该io测试脚本的shell的当前路径里，为了将其保存在指定的文件夹中，方便后续的统计，
我们还得先打开那个文件夹再运行对应的脚本，因此，我专门写了个脚本来生成运行指定的io_workload的命令，从而能够简化流程。

.. code-block:: python

    type = 'USB_Netac_U185/ShortTerm'
    step = 'Step1'
    device = 'DeviceA'
    vibration = 'NoVibration'
    run = 'Run3'
    dst = f'data/{type}/{step}_{device}_{vibration}_{run}'
    io_workload(dst)

我们首先设定一些存放的文件夹的相关信息：

- type指定设备类别以及短时间或者长时间运行
- step指定当前位于实验的哪一步骤
- device指定设备编号，我们有多个设备，有的要经过振动，有的不经过振动，也有可能有其它的区别，因此同一类型的设备也得进行区分
- vibration指定有没有振动
- run指定是第几轮测试，我们一般进行多轮测试取平均结果，这样比较稳定

.. code-block:: python

    def io_workload(dst):
        FT.mkPath(dst)
        print(f'cd /home/liudeyuan/Desktop/vibration/{dst}')

        filename = '/dev/sdb'
        rw = 'randread'
        size = '100%'
        numjobs = 1
        loops = 10
        runtime = '3h'

        cmd = f'sudo -S filename={filename} rw={rw} size={size} numjobs={numjobs} loops={loops} runtime={runtime} fio --output=fio_stats.log /home/liudeyuan/Desktop/vibration/01_io_workload < /home/liudeyuan/Desktop/vibration/password'
        print(cmd)

然后我们指定一些之前提到的io测试的相关信息，运行该脚本就会自动创建文件夹，输出打开文件夹和运行io测试的命令，
然后复制粘贴该脚本输出的命令就可以进行对应的io测试。


示例
--------------

首先，运行01_io_workload.py：

.. code-block:: shell

    python 01_io_workload.py

该脚本会输出后续io测试的命令，复制运行：

.. code-block:: shell

    cd /home/liudeyuan/Desktop/vibration/data/USB_Netac_U185/ShortTerm/Step1_DeviceA_NoVibration_Run3
    sudo -S filename=/dev/sdb rw=randread size=100% numjobs=1 loops=10 runtime=3h fio --output=fio_stats.log /home/liudeyuan/Desktop/vibration/01_io_workload < /home/liudeyuan/Desktop/vibration/password

运行结束之后，便得到了io测试的统计数据。需要注意的是，有时候io测试到了指定时间却停不下来，具体原因是什么我也不是很清楚，可以手动取消，
多进行一些运行尝试。
