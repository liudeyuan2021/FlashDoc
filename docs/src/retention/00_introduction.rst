Introduction
============

Flash的数据保存久了之后可能会出错，QLC Flash的期望出错时间则更短，一些前置知识可查看 `Data Retention in MLC NAND Flash Memory:  Characterization, Optimization, and Recovery <https://github.com/liudeyuan2021/FlashDoc/tree/master/resources/paper/Data_Retention_in_MLC_NAND_Flash_Memory.pdf>`_。
为此，我进行了一些Retention的测试，希望能够测试不同的QLC Flash在不同的P/E Cycle下数据能够保存多长的时间，从而可以进一步地做出改进。

另外，如果我们测试的Flash放置在常温中，则所需的测试时间太长了，因此，我们需要一个恒温箱来使得Flash能够放置在高温环境中，从而加速数据丢失的过程。但是，这又引入了一个新问题，某一高温和常温的数据丢失时间如何对应，
关于这方面可查看 `How the Common Retention Acceleration Method of
3D NAND Flash Memory Goes Wrong? <https://github.com/liudeyuan2021/FlashDoc/tree/master/resources/paper/https://github.com/liudeyuan2021/FlashDoc/blob/master/resources/paper/How_the_Common_Retention_Acceleration_Method_of_3D_NAND_Flash_Memory_Goes_Wrong.pdf>`_。
