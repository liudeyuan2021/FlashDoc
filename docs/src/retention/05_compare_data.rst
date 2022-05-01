Compare Data
============

.. code-block:: python

    def compare_data(src, dst, cpp):
        files = FT.getAllFiles(src)
        pbar = tqdm(total=len(files))
        for file in files:
            dst_file = Path(dst) / Path(file).name
            log_file = Path(dst) / Path('log_' + Path(file).name)
            os.system(f'touch {log_file}')
            if cpp:
                cmd = f'cpp/build/diff_bit_log {file} {dst_file} {log_file}'
                run_command(cmd)
            else:
                diff_bit_log(file, dst_file, log_file)
            pbar.update(1)
        pbar.close()


src为初始数据文件位置，dst为读取出来的数据的存放位置，cpp为调用C++代码的flag。因为比较数据不是调用dd命令运行的，
我编写了C++代码来进行加速。cpp/build/diff_bit_log为C++函数的编译结果，diff_bit_log函数为Python版本的函数。

.. code-block:: python

    def compute_uber(src, dst):
        total = 0
        files = FT.getAllFiles(src)
        for file in files:
            total += int(os.path.getsize(file)) * 8

        error_total = 0
        dst_files = FT.getAllFiles(dst)
        log_files = [file for file in dst_files if file.count('log')]
        for log_file in log_files:
            log = open(log_file)
            error_total += len(log.readlines())

        uber_file = Path(dst) / Path('0_UBER')
        os.system(f'touch {uber_file}')
        uber = open(uber_file, 'w')
        uber.write(f'error_total:{error_total:e} total:{total:e} UBER:{float(error_total)/total:e}')

在C++代码比较了所有的文件之后，会有对应的log文件，我们需要统计对应的log文件的结果，并整合到0_UBER文件中，这样命名可以使得该
文件位于第一个，方便查看。

.. code-block:: python

    def clear_data(src, dst):
        files = FT.getAllFiles(src)
        for i, file in enumerate(files):
            dst_file = Path(dst) / Path(file).name
            cmd = f'sudo -S rm {dst_file} < password'
            run_command(cmd)

在对比完数据之后，我们把对应的数据删除掉，因为我们每次测试的设备至少为6个，每一轮测试的数据量都非常大，如果不及时删除根本村放不下。

.. code-block:: python

    pe_cycle = 50
    retention = 30

同样地，我们首先需要先指定一些全局信息，如上述代码中进行数据比较的6个设备为50P/E Cycle，数据存放的时间为30天。

.. code-block:: python

    def intel_670p():
        type = 'SSD_Intel_670P'
        capacity = 768
        device = '/dev/nvme1n1'

        src = f'data/{capacity}G'
        dst = f'data/{type}/{pe_cycle:03d}pe_{retention:02d}day_{capacity:03d}g'
        compare_data(src, dst, cpp=True)
        compute_uber(src, dst)
        clear_data(src, dst)


    def kingston_nv1():
        type = 'SSD_Kingston_NV1'
        capacity = 768
        device = '/dev/nvme3n1'

        src = f'data/{capacity}G'
        dst = f'data/{type}/{pe_cycle:03d}pe_{retention:02d}day_{capacity:03d}g'
        compare_data(src, dst, cpp=True)
        compute_uber(src, dst)
        clear_data(src, dst)


    def seagate_q5():
        type = 'SSD_Seagate_Q5'
        capacity = 384
        device = '/dev/nvme2n1'

        src = f'data/{capacity}G'
        dst = f'data/{type}/{pe_cycle:03d}pe_{retention:02d}day_{capacity:03d}g'
        compare_data(src, dst, cpp=True)
        compute_uber(src, dst)
        clear_data(src, dst)


    def samsung_870qvo():
        type = 'SSD_SAMSUNG_870QVO'
        capacity = 768
        device = '/dev/sdb'

        src = f'data/{capacity}G'
        dst = f'data/{type}/{pe_cycle:03d}pe_{retention:02d}day_{capacity:03d}g'
        compare_data(src, dst, cpp=True)
        compute_uber(src, dst)
        clear_data(src, dst)


    def sandisk_redgrey():
        type = 'TF_SanDisk_RedGrey'
        capacity = 96
        device = '/dev/sdd'

        src = f'data/{capacity}G'
        dst = f'data/{type}/{pe_cycle:03d}pe_{retention:02d}day_{capacity:03d}g'
        compare_data(src, dst, cpp=True)
        compute_uber(src, dst)
        clear_data(src, dst)


    def netac_u185():
        type = 'USB_Netac_U185'
        capacity = 96
        device = '/dev/sdc'

        src = f'data/{capacity}G'
        dst = f'data/{type}/{pe_cycle:03d}pe_{retention:02d}day_{capacity:03d}g'
        compare_data(src, dst, cpp=True)
        compute_uber(src, dst)
        clear_data(src, dst)

同样地，我们为6种设备分别定义了对应的函数， **还是一样地，对应设备的设备信息要记得修改**。我们分别进行了数据比较、整合log、清除数据。

.. code-block:: shell

    bash tmux.sh

.. code-block:: shell

    python 05_compare_data.py 0
    python 05_compare_data.py 1
    python 05_compare_data.py 2
    python 05_compare_data.py 3
    python 05_compare_data.py 4
    python 05_compare_data.py 5

最后，我们先运行tmux自动切分6个窗口的脚本，然后分别复制粘贴输入对应的python运行命令，就可以同时进行6个设备的比较了。