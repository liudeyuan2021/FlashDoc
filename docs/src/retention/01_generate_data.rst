Generate Data
=============

.. code-block:: shell

    ; example -- fio 01_generate_data
    [writers]
    directory=/home/liudeyuan/Desktop/retention/data/384G
    rw=write
    direct=1
    bs=4kb
    size=48gb
    nrfiles=4
    ioengine=libaio
    iodepth=16
    thread
    numjobs=8

我们使用FIO进行初始数据的生成，上面的代码总共生成了384G的数据，每一个job生成48G数据(size=48gb)、文件数为4个(nrfiles=4)、
共有8个job(numjobs=8)，因此生成的是一个个12G的文件(48 / 4 = 12)，总共生成了384G数据(48 * 8 = 384)。

96G和768G数据的生成过程类似，在此不再赘述。