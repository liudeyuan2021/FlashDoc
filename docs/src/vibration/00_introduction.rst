Introduction
============

Flash的读写带宽和延迟可能会收到振动的影响，我们希望能够复现 `What does Vibration do to Your SSD? <https://github.com/liudeyuan2021/FlashDoc/blob/master/resources/paper/What_does_Vibration_do_to_Your_SSD.pdf>`_ 中的结果，
特别地，针对QLC Flash进行测试。

测试步骤
--------------

1. IO Workload。我们需要进行不同类型的读写测试，短时间或者长时间的，有振动或无振动的，振动前或振动后的，从而得到一批统计数据。

2. P/E Cycle。当我们进行完一轮测试之后，我们需要给所有设备刷一下P/E Cycle，这样才可以测试不同P/E Cycle对测试的影响。

3. Compare Data。我们得到IO测试的统计数据后，需要对数据进行处理，比如对比两批数据生成重要的统计信息和比较图。

4. Trim Data。一轮IO测试之后，需要对SSD进行Trim操作，使得测试前的初始状态可以一致，排除其它因素的干扰。

代码结构
--------------

.. figtable::
    :label: table-xml-objects
    :caption: A high-level overview.

    .. list-table::
        :widths: 17 53 30
        :header-rows: 1

        * - Directory or Script
          - Description
          - example
        * - data
          - 存放IO测试统计信息，存放不同批次测试的比较结果
          - None
        * - tool
          - Python工具脚本，主要为文件和多进程相关
          - None
        * - 01_io_workload
          - io测试脚本
          - fio 01_io_workload
        * - 01_io_workload.py
          - 生成运行io测试脚本的命令
          - python 01_io_workload.py
        * - 02_pe_cycle
          - 刷p/e cycle脚本
          - fio 02_pe_cycle
        * - 03_compare_data.py
          - 比较数据脚本
          - python 03_compare_data.py
        * - 04_trim_data.sh
          - 清除数据脚本
          - bash 04_trim_data.sh
