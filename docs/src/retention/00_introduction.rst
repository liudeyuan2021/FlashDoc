Introduction
============

Flash的数据保存久了之后可能会出错，QLC Flash的期望出错时间则更短，一些前置知识可查看 `Data Retention in MLC NAND Flash Memory:  Characterization, Optimization, and Recovery <https://github.com/liudeyuan2021/FlashDoc/tree/master/resources/paper/Data_Retention_in_MLC_NAND_Flash_Memory.pdf>`_。
为此，我进行了一些Retention的测试，希望能够测试不同的QLC Flash在不同的P/E Cycle下数据能够保存多长的时间，从而可以进一步地做出改进。

另外，如果我们测试的Flash放置在常温中，则所需的测试时间太长了，因此，我们需要一个恒温箱来使得Flash能够放置在高温环境中，从而加速数据丢失的过程。但是，这又引入了一个新问题，某一高温和常温的数据丢失时间如何对应，
关于这方面可查看 `How the Common Retention Acceleration Method of
3D NAND Flash Memory Goes Wrong? <https://github.com/liudeyuan2021/FlashDoc/blob/master/resources/paper/How_the_Common_Retention_Acceleration_Method_of_3D_NAND_Flash_Memory_Goes_Wrong.pdf>`_。

测试步骤
--------------

1. Generate Data。我们需要先生成数据，这些数据之后要写入到Flash中。我们购买的设备有128G、512G、1024G这3种不同的存储容量，因此，我也生成了3批数据，数据量分别为96G、384G、768G，这些数据由一个个12G的文件组成。

2. P/E Cycle。当我们进行完一轮测试之后，我们需要给所有设备刷一下P/E Cycle，这样才可以测试不同P/E Cycle对数据保存时间的影响。

3. Store Data。我们需要将数据写入设备中。

4. Load Data。在恒温箱放置一段时间后，我们需要再把数据读取出来。

5. Compare Data。最后，我们需要将初始数据和保存一段的数据进行比较，并保存比较结果。

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
        * - .vscode
          - vscode配置文件，主要为CMake相关配置
          - None
        * - cpp
          - C++代码，主要为比较数据并保存比较结果的代码
          - None
        * - data
          - 暂时存放读取的数据、存放各轮测试比较结果
          - None
        * - stas
          - 存放生成数据的统计信息，主要为二进制位1占所有二进制位数的比率
          - None
        * - tool
          - Python工具脚本，主要为文件和多进程相关
          - None
        * - 01_generate_data
          - 生成数据脚本
          - fio 01_generate_data
        * - 02_pe_cycle
          - 刷p/e cycle脚本
          - fio 02_pe_cycle
        * - 03_store_data.py
          - 写入数据脚本
          - python 03_store_data.py
        * - 04_load_data.py
          - 读取数据脚本
          - python 04_load_data.py
        * - 05_compare_data.py
          - 比较数据脚本
          - python 05_compare_data.py
        * - tmux.sh
          - tmux自动切分6个框脚本，方便测试
          - bash tmux.sh

